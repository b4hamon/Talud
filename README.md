# Biblioteca Central - Sistema de Gestión con Docker Compose

## Descripción del Proyecto

Este proyecto implementa un sistema de gestión de biblioteca completamente funcional utilizando Docker Compose. El stack está compuesto por cuatro servicios que se comunican entre sí a través de una red interna de Docker:

- **PostgreSQL** - Base de datos relacional con datos persistentes
- **Flask (Python)** - API REST con operaciones CRUD
- **pgAdmin** - Panel de administración de la base de datos
- **Nginx** - Servidor web que sirve el frontend HTML/JS

## Arquitectura

```
┌─────────────────────────────────────────────────────────────┐
│                        HOST                                 │
│                                                              │
│   ┌──────────────┐  ┌──────────────┐  ┌──────────────┐    │
│   │  Puerto 3000 │  │  Puerto 5000 │  │  Puerto 8080 │    │
│   │  (Frontend)  │  │    (API)     │  │  (pgAdmin)    │    │
│   └──────┬───────┘  └──────┬───────┘  └──────┬───────┘    │
│          │                  │                  │            │
│          ▼                  ▼                  ▼            │
│   ┌─────────────────────────────────────────────────────┐  │
│   │              Red interna: 777-network               │  │
│   │                                                      │  │
│   │   ┌─────────┐  ┌─────────┐  ┌─────────┐           │  │
│   │   │ Nginx   │  │  Flask  │  │ pgAdmin │           │  │
│   │   │ frontend│◄─┤   web   │  │         │           │  │
│   │   └─────────┘  └────┬────┘  └─────────┘           │  │
│   │                     │                             │  │
│   │                ┌────▼────┐                        │  │
│   │                │   db    │                        │  │
│   │                │PostgreSQL│                       │  │
│   │                └─────────┘                        │  │
│   │                                                      │  │
│   └─────────────────────────────────────────────────────┘  │
│                         Docker Compose                       │
└─────────────────────────────────────────────────────────────┘
```

## Requisitos Previos

- Docker Engine 20.10+
- Docker Compose 2.0+

Para verificar que Docker y Docker Compose están correctamente instalados, ejecuta:

```bash
docker --version
docker compose version
```

![IMG1](IMG1.png)
_Descripción: Captura de la terminal mostrando las versiones instaladas de Docker Engine y Docker Compose, confirmando que cumplen con los requisitos mínimos (20.10+ y 2.0+ respectivamente)._

---

## Configuración

Las variables de entorno se encuentran en el archivo `.env`:

```env
# Configuración PostgreSQL
POSTGRES_USER=prueba
POSTGRES_PASSWORD=prueba_pass
POSTGRES_DB=biblioteca_db
DB_HOST=db
DB_PORT=5432
# Configuración pgAdmin
PGADMIN_DEFAULT_EMAIL=admin@biblioteca.com
PGADMIN_DEFAULT_PASSWORD=admin_pass
```

![IMG2](IMG2.png)
_Descripción: Captura del archivo `.env` abierto en un editor de texto o visualizado en la terminal con `cat .env`, mostrando todas las variables de entorno definidas para el proyecto._

---

## Instrucciones de Uso

### 1. Construir y levantar el stack

```bash
docker-compose up --build
```

![IMG3](IMG3.png)
_Descripción: Captura de la terminal durante la ejecución de `docker-compose up --build`, mostrando el proceso de descarga de imágenes base, construcción de los contenedores Flask y Nginx, y los logs iniciales de arranque de todos los servicios._

![IMG4](IMG4.png)
_Descripción: Captura de la terminal mostrando el estado final del arranque con todos los servicios corriendo y el mensaje de la base de datos lista (healthcheck completado), indicando que el stack se levantó correctamente._

---

### 2. Verificar el estado de los servicios

```bash
docker-compose ps
```

Debería mostrar todos los servicios en estado `running` o `healthy`.

![IMG5](IMG5.png)
_Descripción: Captura de la terminal con la salida del comando `docker-compose ps`, mostrando los cuatro contenedores (`biblioteca_db`, `biblioteca_backend`, `biblioteca_pgadmin`, `biblioteca_frontend`) con sus estados `running` o `healthy`, puertos mapeados y nombres._

---

### 3. Acceder a los servicios

| Servicio | URL | Credenciales |
|----------|-----|--------------|
| Frontend | http://localhost:3000 | - |
| API REST | http://localhost:5000 | - |
| pgAdmin | http://localhost:8080 | admin@biblioteca.com / admin_pass |

#### Frontend (http://localhost:3000)

![IMG6](IMG6.png)
_Descripción: Captura del navegador web abierto en `http://localhost:3000`, mostrando la interfaz principal de la Biblioteca Central con sus pestañas (Autores, Libros, Géneros, Usuarios) y los datos cargados en las tablas._

#### API REST (http://localhost:5000)

![IMG7](IMG7.png)
_Descripción: Captura del navegador o cliente REST (como Postman/Insomnia) accediendo a `http://localhost:5000`, mostrando la respuesta JSON con el mensaje de bienvenida y la lista de endpoints disponibles._

#### pgAdmin (http://localhost:8080)

![IMG8](IMG8.png)
_Descripción: Captura del navegador en `http://localhost:8080` mostrando la pantalla de inicio de sesión de pgAdmin con los campos de Email y Contraseña listos para ingresar las credenciales `admin@biblioteca.com` / `admin_pass`._

---

### 4. Detener el stack (sin eliminar volúmenes)

```bash
docker-compose down
```

![IMG9](IMG9.png)
_Descripción: Captura de la terminal ejecutando `docker-compose down`, mostrando el proceso de detención y eliminación de los contenedores, con confirmación de que los volúmenes de datos se conservan intactos._

---

### 5. Reiniciar desde cero (elimina datos)

```bash
docker-compose down -v
docker-compose up --build
```

![IMG10](IMG10.png)
_Descripción: Captura de la terminal mostrando primero la ejecución de `docker-compose down -v` con el mensaje de eliminación de volúmenes, seguido del inicio de `docker-compose up --build` con la reconstrucción completa del stack desde cero._

---

### 6. Ver logs de un servicio específico

```bash
docker-compose logs -f web
```

![IMG11](IMG11.png)
_Descripción: Captura de la terminal mostrando la salida en tiempo real de los logs del servicio `web` (Flask), incluyendo las solicitudes HTTP recibidas, respuestas enviadas y mensajes del servidor de desarrollo._

---

## API Endpoints

La API REST proporciona las siguientes entidades:

### Autores
- `GET /autores` - Listar todos los autores
- `GET /autores/<id>` - Obtener un autor específico
- `POST /autores` - Crear un nuevo autor
- `PUT /autores/<id>` - Actualizar un autor
- `DELETE /autores/<id>` - Eliminar un autor

![IMG12](IMG12.png)
_Descripción: Captura de una herramienta cliente de API (Postman, Insomnia o `curl` en terminal) ejecutando una petición `GET /autores`, mostrando la respuesta JSON con el listado completo de autores almacenados en la base de datos._

![IMG13](IMG13.png)
_Descripción: Captura de una herramienta cliente de API realizando una petición `POST /autores` con el cuerpo JSON de ejemplo y la respuesta exitosa del servidor (código 201) con los datos del autor recién creado._

---

### Libros
- `GET /libros` - Listar todos los libros
- `POST /libros` - Crear un nuevo libro
- `PUT /libros/<id>` - Actualizar un libro
- `DELETE /libros/<id>` - Eliminar un libro

![IMG14](IMG14.png)
_Descripción: Captura de una petición `GET /libros` mostrando la respuesta JSON con el catálogo completo de libros, incluyendo los campos `id`, `titulo`, `id_autor` y `anio_publicacion`._

---

### Géneros
- `GET /generos` - Listar todos los géneros
- `POST /generos` - Crear un nuevo género
- `PUT /generos/<id>` - Actualizar un género
- `DELETE /generos/<id>` - Eliminar un género

![IMG15](IMG15.png)
_Descripción: Captura de una petición `GET /generos` mostrando la respuesta JSON con todos los géneros literarios registrados, incluyendo sus campos `id`, `nombre` y `descripcion`._

---

### Usuarios
- `GET /usuarios` - Listar todos los usuarios
- `POST /usuarios` - Crear un nuevo usuario
- `PUT /usuarios/<id>` - Actualizar un usuario
- `DELETE /usuarios/<id>` - Eliminar un usuario

![IMG16](IMG16.png)
_Descripción: Captura de una petición `GET /usuarios` mostrando la respuesta JSON con el listado de usuarios registrados, incluyendo `id`, `nombre`, `email`, `telefono` y `fecha_registro`._

---

## Capturas de Pantalla

### 1. Servicios ejecutándose

```
docker-compose ps
```

Debería mostrar:
- biblioteca_db        → running (healthy)
- biblioteca_backend    → running
- biblioteca_pgadmin    → running
- biblioteca_frontend   → running

![IMG17](IMG17.png)
_Descripción: Captura de la terminal con la tabla completa de salida de `docker-compose ps`, donde se aprecian los cuatro servicios activos con su estado `running (healthy)` o `running`, los puertos mapeados al host y los nombres de contenedor asignados._

---

### 2. API respondiendo

```bash
curl http://localhost:5000/
```

Respuesta esperada:
```json
{
  "mensaje": "API de Gestión de Biblioteca en funcionamiento",
  "endpoints": {
    "autores": "/autores",
    "libros": "/libros",
    "generos": "/generos",
    "usuarios": "/usuarios",
    "documentacion": "CRUD completo habilitado para todas las entidades"
  }
}
```

![IMG18](IMG18.png)
_Descripción: Captura de la terminal ejecutando el comando `curl http://localhost:5000/` y mostrando la respuesta JSON formateada con el mensaje de estado de la API y los endpoints disponibles, tal como se indica en la respuesta esperada._

---

### 3. Frontend funcionando

Acceder a http://localhost:3000 y verificar que:
- Se muestra la interfaz de la Biblioteca Central
- Hay pestañas para Autores, Libros, Géneros y Usuarios
- Se pueden ver los datos en tablas
- Los botones "Nuevo registro" y "Editar" funcionan

![IMG19](IMG19.png)
_Descripción: Captura del navegador mostrando la interfaz completa de la Biblioteca Central en `http://localhost:3000`, con las cuatro pestañas visibles en la barra de navegación y la pestaña activa mostrando datos en una tabla con los botones "Nuevo registro" y "Editar" operativos._

![IMG20](IMG20.png)
_Descripción: Captura del modal o formulario que se despliega al hacer clic en "Nuevo registro" o "Editar" dentro del frontend, mostrando los campos del formulario listos para ingresar o modificar datos._

---

### 4. pgAdmin conectado

Acceder a http://localhost:8080 e iniciar sesión con:
- Email: admin@biblioteca.com
- Contraseña: admin_pass

![IMG21](IMG21.png)
_Descripción: Captura de la pantalla principal de pgAdmin tras iniciar sesión correctamente, mostrando el panel de control con el árbol de servidores en el panel izquierdo._

Conectar al servidor PostgreSQL:
- Host: db
- Puerto: 5432
- Usuario: prueba
- Contraseña: prueba_pass
- Base de datos: biblioteca_db

![IMG22](IMG22.png)
_Descripción: Captura del diálogo de conexión a servidor en pgAdmin con los campos completados (Host: `db`, Puerto: `5432`, Usuario: `prueba`, Base de datos: `biblioteca_db`) antes de hacer clic en "Save/Guardar"._

Verificar que existen las tablas: `autores`, `libros`, `prestamos`, `generos`, `usuarios`.

![IMG23](IMG23.png)
_Descripción: Captura del explorador de objetos en pgAdmin con la base de datos `biblioteca_db` expandida, mostrando el nodo "Tables" con las cinco tablas listadas: `autores`, `libros`, `prestamos`, `generos` y `usuarios`._

---

## Estructura del Proyecto

```
taller_docker/
├── .env                           # Variables de entorno
├── docker-compose.yml            # Orquestación de servicios
├── README.md                      # Documentación
├── app/
│   ├── Dockerfile                 # Dockerfile de Flask
│   ├── app.py                     # Código de la API REST
│   ├── requirements.txt           # Dependencias de Python
│   └── init-db/
│       └── 01-init.sql            # Script de inicialización de DB
└── frontend/
    ├── Dockerfile                 # Dockerfile de Nginx
    ├── nginx.conf                 # Configuración de proxy inverso
    └── index.html                 # Interfaz de usuario
```

![IMG24](IMG24.png)
_Descripción: Captura del explorador de archivos del sistema operativo o del terminal ejecutando `tree taller_docker/` (o `find . -not -path '*/\.*'`), mostrando la estructura de directorios y archivos del proyecto tal como se describe arriba._

---

## Base de Datos

El script de inicialización crea las siguientes tablas:

1. **autores** - Información de autores (id, nombre, nacionalidad)
2. **libros** - Catálogo de libros (id, titulo, id_autor, anio_publicacion)
3. **prestamos** - Registro de préstamos (id, id_libro, nombre_usuario, fecha_prestamo)
4. **generos** - Géneros literarios (id, nombre, descripcion)
5. **usuarios** - Usuarios registrados (id, nombre, email, telefono, fecha_registro)

Las relaciones entre tablas se establecen mediante claves foráneas:
- `libros.id_autor` → `autores.id`
- `prestamos.id_libro` → `libros.id`

![IMG25](IMG25.png)
_Descripción: Captura del Query Tool de pgAdmin o de la terminal mostrando el resultado de un `SELECT` o `\dt` sobre `biblioteca_db`, listando las cinco tablas con sus columnas y tipos de datos, evidenciando la estructura del esquema creado por `01-init.sql`._

![IMG26](IMG26.png)
_Descripción: Captura del diagrama entidad-relación (ERD) en pgAdmin (generado desde Tools → ERD Tool) mostrando las cinco tablas con sus columnas y las relaciones de clave foránea entre `libros.id_autor → autores.id` y `prestamos.id_libro → libros.id`._

---

## Buenas Prácticas Implementadas

- ✅ Credenciales almacenadas únicamente en archivo `.env`
- ✅ Contenedor de base de datos sin puertos expuestos al host
- ✅ Nombres de contenedores explícitos
- ✅ Healthcheck para garantizar que la DB esté lista
- ✅ Uso de `depends_on` con `condition: service_healthy`
- ✅ Red y volúmenes nombrados explícitamente

![IMG27](IMG27.png)
_Descripción: Captura del archivo `docker-compose.yml` abierto en un editor o visualizado en la terminal, resaltando las secciones relevantes: la directiva `healthcheck` del servicio `db`, la condición `depends_on: condition: service_healthy` del servicio `web`, la ausencia de mapeo de puertos en el servicio `db`, y la definición de la red y volúmenes nombrados al final del archivo._

---

## Licencia

Este proyecto fue desarrollado para fines educativos.
