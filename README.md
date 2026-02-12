# Entorno de Desarrollo con PostgreSQL y pgAdmin usando Docker Compose

Este proyecto proporciona una configuración de Docker Compose lista para usar, ideal para levantar un entorno de desarrollo con una base de datos PostgreSQL y un cliente de administración pgAdmin.

La configuración está optimizada con buenas prácticas, incluyendo el uso de volúmenes nombrados para la persistencia de datos, variables de entorno para la configuración, y `healthchecks` para asegurar un arranque robusto de los servicios.

## Requisitos

-   [Docker](https://docs.docker.com/get-docker/)
-   [Docker Compose](https://docs.docker.com/compose/install/) (generalmente incluido con Docker Desktop)

## Puesta en Marcha

1.  **Clonar el Repositorio (si aplica)**

    ```bash
    git clone <URL_DEL_REPOSITORIO>
    cd postgresql-docker-compose
    ```

2.  **Configurar Variables de Entorno**

    Este proyecto utiliza un archivo `.env` para gestionar las credenciales y la configuración. Puedes empezar copiando el archivo de ejemplo:

    ```bash
    # En Windows (Command Prompt)
    copy .env.example .env

    # En Windows (PowerShell)
    Copy-Item .env.example .env

    # En Linux o macOS
    cp .env.example .env
    ```

    Abre el archivo `.env` y personaliza las variables. Como mínimo, deberás establecer:
    -   `POSTGRES_DB_USER`: Usuario para la base de datos.
    -   `POSTGRES_DB_PASSWORD`: Contraseña para el usuario de la base de datos.
    -   `POSTGRES_DB_DEFAULT`: Nombre de la base de datos a crear.
    -   `PGADMIN_DEFAULT_EMAIL`: Email para iniciar sesión en pgAdmin.
    -   `PGADMIN_DEFAULT_PASSWORD`: Contraseña para pgAdmin.

3.  **Levantar los Servicios**

    Usa Docker Compose para construir y levantar los contenedores en segundo plano (`-d`):

    ```bash
    docker-compose up -d
    ```

    La primera vez, Docker descargará las imágenes necesarias, lo que puede tardar unos minutos.

4.  **Verificar los Contenedores**

    Puedes comprobar que los contenedores están en funcionamiento con:

    ```bash
    docker-compose ps
    ```

    Deberías ver los servicios `postgresql` y `pgadmin` con el estado `running` o `healthy`.

## Acceso a los Servicios

-   **PostgreSQL:**
    -   **Host:** `localhost`
    -   **Puerto:** `5432`
    -   **Usuario:** El que definiste en `POSTGRES_DB_USER`
    -   **Contraseña:** La que definiste en `POSTGRES_DB_PASSWORD`
    -   **Base de datos:** La que definiste en `POSTGRES_DB_DEFAULT`

-   **pgAdmin (Cliente Web):**
    -   **URL:** [http://localhost:5050](http://localhost:5050)
    -   **Usuario:** El que definiste en `PGADMIN_DEFAULT_EMAIL`
    -   **Contraseña:** La que definiste en `PGADMIN_DEFAULT_PASSWORD`

    Una vez que inicies sesión en pgAdmin, necesitarás registrar un nuevo servidor para conectarte a tu base de datos PostgreSQL usando las credenciales anteriores.

## Inicialización de la Base de Datos

Este proyecto incluye un mecanismo para ejecutar scripts SQL al iniciar la base de datos por primera vez.

-   Coloca tus archivos `.sql`, `.sh` o `.sql.gz` en el directorio `docker-entrypoint-initdb.d` (puedes crearlo si no existe).
-   En esta configuración, el archivo `docker-db-init.sql` se monta en ese directorio dentro del contenedor.
-   Cualquier script en `docker-entrypoint-initdb.d` se ejecutará en orden alfabético la **primera vez** que el contenedor de PostgreSQL se inicie y cree la base de datos.

## Gestión de Datos (Persistencia)

-   **PostgreSQL:** Los datos de la base de datos se guardan en un **volumen nombrado** de Docker llamado `postgres_data`. Esto asegura que tus datos persistan incluso si eliminas el contenedor.
-   **pgAdmin:** La configuración de pgAdmin (servidores, preferencias, etc.) se guarda en un volumen nombrado llamado `pgadmin_data`.

Para listar los volúmenes, puedes usar:
```bash
docker volume ls
```

Si deseas eliminar todo, incluyendo los volúmenes (¡CUIDADO: ESTO BORRARÁ TODOS TUS DATOS!), puedes usar:
```bash
docker-compose down -v
```

## Detener el Entorno

Para detener los contenedores sin eliminar los datos, ejecuta:

```bash
docker-compose down
```
