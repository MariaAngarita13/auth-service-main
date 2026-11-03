# Auth Service – Servicio de Autenticación

## Descripción

Auth Service es un microservicio de autenticación desarrollado con **Spring Boot**, encargado de gestionar el registro e inicio de sesión de los usuarios dentro de una arquitectura de microservicios.

El servicio permite registrar usuarios, validar credenciales y generar tokens JWT (JSON Web Token) para gestionar la autenticación de forma segura.

Este proyecto hace parte del Laboratorio 4 de Sistemas Distribuidos de la UPTC.

## Tecnologías utilizadas

- **Java 21:** lenguaje de programación.
- **Spring Boot 3.4.3:** framework para el desarrollo del servicio.
- **Spring Data JPA:** persistencia y acceso a datos.
- **PostgreSQL:** sistema gestor de bases de datos.
- **JWT:** generación y validación de tokens de autenticación.
- **BCrypt:** cifrado de contraseñas.
- **Swagger / OpenAPI:** documentación y pruebas de los endpoints.
- **Maven:** gestión de dependencias y construcción del proyecto.
- **Docker:** contenedorización del microservicio.

## Funcionalidades principales

- Registro de nuevos usuarios.
- Inicio de sesión mediante usuario y contraseña.
- Validación de credenciales.
- Encriptación de contraseñas utilizando BCrypt.
- Generación de tokens JWT.
- Asignación de roles a los usuarios.
- Asociación de usuarios estudiantes con su identificador de estudiante.
- Manejo centralizado de excepciones.
- Validación de los datos recibidos mediante solicitudes HTTP.

## Roles disponibles

El sistema contempla dos tipos de usuarios:

| Rol | Descripción |
|---|---|
| ADMIN | Administrador del sistema. |
| ESTUDIANTE | Usuario con rol de estudiante. |

## Estructura del proyecto

```text
auth-service/
│
├── src/
│   └── main/
│       ├── java/
│       │   └── co/edu/uptc/auth/
│       │       │
│       │       ├── config/
│       │       │   ├── DataSeeder.java
│       │       │   └── SecurityConfig.java
│       │       │
│       │       ├── controller/
│       │       │   └── AuthController.java
│       │       │
│       │       ├── dto/
│       │       │   ├── AuthResponse.java
│       │       │   ├── LoginRequest.java
│       │       │   └── RegisterRequest.java
│       │       │
│       │       ├── entity/
│       │       │   ├── Role.java
│       │       │   └── User.java
│       │       │
│       │       ├── exception/
│       │       │   ├── BadCredentialsException.java
│       │       │   ├── ErrorResponse.java
│       │       │   ├── GlobalExceptionHandler.java
│       │       │   └── UserAlreadyExistsException.java
│       │       │
│       │       ├── repository/
│       │       │   └── UserRepository.java
│       │       │
│       │       ├── service/
│       │       │   ├── AuthService.java
│       │       │   └── JwtService.java
│       │       │
│       │       └── AuthServiceApplication.java
│       │
│       └── resources/
│           └── application.yml
│
├── .mvn/
├── Dockerfile
├── mvnw
├── mvnw.cmd
└── pom.xml
```

## Configuración e instalación

### 1. Requisitos previos

Antes de ejecutar el proyecto, es necesario contar con:

- Java JDK 21.
- Maven.
- PostgreSQL.
- Git (opcional, para clonar el repositorio).

### 2. Clonar el repositorio

```bash
git clone URL_DEL_REPOSITORIO
```

Ingresar a la carpeta:

```bash
cd auth-service
```

### 3. Configurar la base de datos

Crear una base de datos en PostgreSQL con el nombre:

```sql
CREATE DATABASE auth_db;
```

El proyecto utiliza las siguientes variables de configuración:

| Variable | Valor predeterminado |
|---|---|
| SERVER_PORT | 8084 |
| DB_HOST | localhost |
| DB_PORT | 5432 |
| DB_NAME | auth_db |
| DB_USERNAME | postgres |
| DB_PASSWORD | postgres |
| JWT_EXPIRATION_MS | 86400000 |

Las variables se pueden modificar según la configuración del entorno de ejecución.

**Importante:** Para ambientes de producción se recomienda configurar una contraseña segura para PostgreSQL y una clave JWT propia, evitando utilizar los valores predeterminados.

### 4. Ejecutar el proyecto

En Windows:

```bash
mvnw.cmd spring-boot:run
```

En Linux o macOS:

```bash
./mvnw spring-boot:run
```

También se puede ejecutar desde un IDE como IntelliJ IDEA o Eclipse, iniciando la clase `AuthServiceApplication`.

El servicio estará disponible en:

```text
http://localhost:8084
```

## Endpoints disponibles

El microservicio expone los siguientes endpoints:

| Método HTTP | Endpoint | Descripción |
|---|---|---|
| POST | `/api/auth/register` | Registrar un usuario. |
| POST | `/api/auth/login` | Iniciar sesión. |
| GET | `/actuator/health` | Consultar el estado del servicio. |

### 1. Registro de usuarios

**Endpoint:**

```http
POST /api/auth/register
```

**Ejemplo de solicitud:**

```json
{
  "username": "estudiante3@uptc.edu.co",
  "password": "Student123*",
  "fullName": "Carlos Rodríguez",
  "role": "ESTUDIANTE",
  "studentId": 3
}
```

**Respuesta esperada:** HTTP 201 Created.

El sistema registra el usuario, almacena su contraseña utilizando BCrypt y genera un token JWT.

### 2. Inicio de sesión

**Endpoint:**

```http
POST /api/auth/login
```

**Ejemplo de solicitud:**

```json
{
  "username": "estudiante3@uptc.edu.co",
  "password": "Student123*"
}
```

**Respuesta esperada:** HTTP 200 OK.

La respuesta contiene:

- Token JWT.
- Tipo de token (Bearer).
- Tiempo de expiración.
- Nombre de usuario.
- Nombre completo.
- Rol del usuario.
- Identificador del estudiante, cuando corresponda.

### 3. Estado del servicio

**Endpoint:**

```http
GET /actuator/health
```

Permite consultar el estado de funcionamiento del microservicio.

## Usuarios iniciales

Cuando la base de datos no contiene usuarios, el sistema crea automáticamente tres usuarios de prueba:

| Usuario | Contraseña | Rol |
|---|---|---|
| admin@uptc.edu.co | Admin123* | ADMIN |
| estudiante1@uptc.edu.co | Student123* | ESTUDIANTE |
| estudiante2@uptc.edu.co | Student123* | ESTUDIANTE |

Estos usuarios son creados por la clase `DataSeeder`.

**Nota:** Son credenciales de desarrollo y pruebas. No deben utilizarse en producción.

## Documentación de la API

El proyecto integra Swagger UI mediante Springdoc OpenAPI.

Una vez iniciado el microservicio, la documentación se puede consultar en:

```text
http://localhost:8084/swagger-ui.html
```

También se puede acceder al documento OpenAPI en:

```text
http://localhost:8084/v3/api-docs
```

Desde Swagger es posible consultar los endpoints y realizar pruebas de las solicitudes HTTP.

## Ejecución con Docker

El proyecto incluye un `Dockerfile` con una construcción de múltiples etapas.

Para construir la imagen:

```bash
docker build -t auth-service .
```

Para ejecutar el contenedor:

```bash
docker run -p 8084:8084 auth-service
```

**Importante:** Para ejecutar el servicio en Docker con una base de datos PostgreSQL, se deben configurar las variables de entorno correspondientes, especialmente `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USERNAME` y `DB_PASSWORD`.

## Manejo de seguridad

El microservicio implementa mecanismos de seguridad para proteger la información de los usuarios:

- BCrypt para almacenar contraseñas de forma segura.
- JWT para la generación de tokens de autenticación.
- Validación de credenciales durante el inicio de sesión.
- Validación de datos de entrada.
- Control de usuarios duplicados.
- Manejo de errores mediante excepciones personalizadas.

## Autor y contexto académico

**Proyecto:** Auth Service – Servicio de Autenticación.

**Institución:** Universidad Pedagógica y Tecnológica de Colombia (UPTC).

**Programa:** Ingeniería de Sistemas y Computación.

**Asignatura:** Sistemas Distribuidos.

---

