# Redmine Installation & deployment

## What is Redmine?

> Redmine is an open source management application. It includes a tracking issue system, Gantt charts for a visual view of projects and deadlines, and supports SCM integration for version control.

[Overview of Redmine](http://www.redmine.org/)

## TL;DR

```bash
git clone https://github.com/Ravilochan/redmine.git
cd redmine
docker compose up -d
```

##### User and Site configuration

- `REDMINE_PORT_NUMBER`: Port number in which Redmine will run. Default: **3000**
- `REDMINE_USERNAME`: Redmine application username. Default: **user**
- `REDMINE_PASSWORD`: Redmine application password. Default: **bitnami1**
- `REDMINE_EMAIL`: Redmine application email. Default: **user@example.com**
- `REDMINE_FIRST_NAME`: Redmine user first name. Default: **UserName**
- `REDMINE_LAST_NAME`: Redmine user last name. Default: **LastName**
- `REDMINE_LANGUAGE`: Redmine site default language. Default: **en**
- `REDMINE_REST_API_ENABLED`: Whether to allow REST API calls to Redmine. Default: **0**
- `REDMINE_LOAD_DEFAULT_DATA`: Whether to load default configuration data for Redmine. Default: **yes**
- `REDMINE_SKIP_BOOTSTRAP`: Whether to skip performing the initial bootstrapping for the application. This is necessary in case you use a database that already has Redmine data. Default: **no**

##### Database connection configuration

- `REDMINE_DATABASE_TYPE`: Database type to be used for the Redmine installation. Allowed values: _mariadb_, _postgresql_. Default: **mariadb**
- `REDMINE_DATABASE_HOST`: Hostname for the MariaDB or MySQL server. Default: **mariadb**
- `REDMINE_DATABASE_PORT_NUMBER`: Port used by the MariaDB or MySQL server. Default: **3306**
- `REDMINE_DATABASE_NAME`: Database name that Redmine will use to connect with the database. Default: **bitnami_redmine**
- `REDMINE_DATABASE_USER`: Database user that Redmine will use to connect with the database. Default: **bn_redmine**
- `REDMINE_DATABASE_PASSWORD`: Database password that Redmine will use to connect with the database. No default.
- `ALLOW_EMPTY_PASSWORD`: It can be used to allow blank passwords. Default: **no**

##### Create a MariaDB or MySQL database for Redmine using mysql-client

- `MYSQL_CLIENT_DATABASE_HOST`: Hostname for the MariaDB or MySQL server. Default: **mariadb**
- `MYSQL_CLIENT_DATABASE_PORT_NUMBER`: Port used by the MariaDB or MySQL server. Default: **3306**
- `MYSQL_CLIENT_DATABASE_ROOT_USER`: Database admin user. Default: **root**
- `MYSQL_CLIENT_DATABASE_ROOT_PASSWORD`: Database password for the database admin user. No default.
- `MYSQL_CLIENT_CREATE_DATABASE_NAME`: New database to be created by the mysql client module. No default.
- `MYSQL_CLIENT_CREATE_DATABASE_USER`: New database user to be created by the mysql client module. No default.
- `MYSQL_CLIENT_CREATE_DATABASE_PASSWORD`: Database password for the `MYSQL_CLIENT_CREATE_DATABASE_USER` user. No default.
- `MYSQL_CLIENT_CREATE_DATABASE_CHARACTER_SET`: Character set to use for the new database. No default.
- `MYSQL_CLIENT_CREATE_DATABASE_COLLATE`: Database collation to use for the new database. No default.
- `MYSQL_CLIENT_ENABLE_SSL`: Whether to enable SSL connections for the new database. Default: **no**
- `MYSQL_CLIENT_SSL_CA_FILE`: Path to the SSL CA file for the new database. No default.
- `ALLOW_EMPTY_PASSWORD`: It can be used to allow blank passwords. Default: **no**

##### Create a PostgreSQL database for Redmine using postgresql-client

- `POSTGRESQL_CLIENT_DATABASE_HOST`: Hostname for the PostgreSQL server. Default: **postgresql**
- `POSTGRESQL_CLIENT_DATABASE_PORT_NUMBER`: Port used by the PostgreSQL server. Default: **5432**
- `POSTGRESQL_CLIENT_POSTGRES_USER`: Database admin user. Default: **root**
- `POSTGRESQL_CLIENT_POSTGRES_PASSWORD`: Database password for the database admin user. No defaults.
- `POSTGRESQL_CLIENT_CREATE_DATABASE_NAMES`: List of new databases to be created by the postgresql-client module. No defaults.
- `POSTGRESQL_CLIENT_CREATE_DATABASE_USER`: New database user to be created by the postgresql-client module. No defaults.
- `POSTGRESQL_CLIENT_CREATE_DATABASE_PASSWORD`: Database password for the `POSTGRESQL_CLIENT_CREATE_DATABASE_USER` user. No defaults.
- `POSTGRESQL_CLIENT_CREATE_DATABASE_EXTENSIONS`: PostgreSQL extensions to enable in the specified database during the first initialization. No defaults.
- `POSTGRESQL_CLIENT_EXECUTE_SQL`: SQL code to execute in the PostgreSQL server. No defaults.
- `ALLOW_EMPTY_PASSWORD`: It can be used to allow blank passwords. Default: **no**

##### SMTP Configuration

To configure Redmine to send email using SMTP you can set the following environment variables:

- `REDMINE_SMTP_HOST`: SMTP host.
- `REDMINE_SMTP_PORT`: SMTP port.
- `REDMINE_SMTP_USER`: SMTP account user.
- `REDMINE_SMTP_PASSWORD`: SMTP account password.
- `REDMINE_SMTP_PROTOCOL`: If specified, SMTP protocol to use. Allowed values: _tls_, _ssl_. No default.
- `REDMINE_SMTP_AUTH`: SMTP authentication method. Allowed values: _login_, _plain_, _cram_md5_. Default: **login**.

#### Examples

##### SMTP configuration using a Gmail account

This would be an example of SMTP configuration using a Gmail account:

- Modify the [`docker-compose.yml`](https://github.com/Ravilochan/redmine/blob/main/docker-compose.yml) file present in this repository:

  ```yaml
    redmine:
      ...
      environment:
        - REDMINE_DATABASE_USER=bn_redmine
        - REDMINE_DATABASE_NAME=bitnami_redmine
        - ALLOW_EMPTY_PASSWORD=yes
        - REDMINE_SMTP_HOST=smtp.gmail.com
        - REDMINE_SMTP_PORT=587
        - REDMINE_SMTP_USER=your_email@gmail.com
        - REDMINE_SMTP_PASSWORD=your_password
    ...
  ```

- For manual execution:

  ```console
  $ docker run -d --name redmine -p 80:8080 -p 443:8443 \
    --env REDMINE_DATABASE_USER=bn_redmine \
    --env REDMINE_DATABASE_NAME=bitnami_redmine \
    --env REDMINE_SMTP_HOST=smtp.gmail.com \
    --env REDMINE_SMTP_PORT=587 \
    --env REDMINE_SMTP_USER=your_email@gmail.com \
    --env REDMINE_SMTP_PASSWORD=your_password \
    --network redmine-tier \
    --volume /path/to/redmine-persistence:/bitnami \
    bitnami/redmine:latest
  ```

##### Connect Redmine container to an existing database

The Bitnami Redmine container supports connecting the Redmine application to an external database. This would be an example of using an external database for Redmine.

- Modify the [`docker-compose.yml`](https://github.com/Ravilochan/redmine/blob/main/docker-compose.yml) file present in this repository:

  ```diff
     redmine:
       ...
       environment:
  -      - REDMINE_DATABASE_HOST=mariadb
  +      - REDMINE_DATABASE_HOST=mariadb_host
         - REDMINE_DATABASE_PORT_NUMBER=3306
         - REDMINE_DATABASE_NAME=redmine_db
         - REDMINE_DATABASE_USER=redmine_user
  -      - ALLOW_EMPTY_PASSWORD=yes
  +      - REDMINE_DATABASE_PASSWORD=redmine_password
       ...
  ```

- For manual execution:

  ```console
  $ docker run -d --name redmine\
    -p 8080:8080 -p 8443:8443 \
    --network redmine-network \
    --env REDMINE_DATABASE_HOST=mariadb_host \
    --env REDMINE_DATABASE_PORT_NUMBER=3306 \
    --env REDMINE_DATABASE_NAME=redmine_db \
    --env REDMINE_DATABASE_USER=redmine_user \
    --env REDMINE_DATABASE_PASSWORD=redmine_password \
    --volume redmine_data:/bitnami/redmine \
    bitnami/redmine:latest
  ```

In case the database already contains data from a previous Redmine installation, you need to set the variable `REDMINE_SKIP_BOOTSTRAP` to `yes`. Otherwise, the container would execute the installation wizard and could modify the existing data in the database. Note that, when setting `REDMINE_SKIP_BOOTSTRAP` to `yes`, values for environment variables such as `REDMINE_USERNAME`, `REDMINE_PASSWORD` or `REDMINE_EMAIL` will be ignored.

## Logging

The Bitnami Redmine Docker image sends the container logs to `stdout`. To view the logs:

```console
$ docker logs redmine
```

Or using Docker Compose:

```console
$ docker-compose logs redmine
```

You can configure the containers [logging driver](https://docs.docker.com/engine/admin/logging/overview/) using the `--log-driver` option if you wish to consume the container logs differently. In the default configuration docker uses the `json-file` driver.

## Maintenance

### Backing up your container

To backup your data, configuration and logs, follow these simple steps:

#### Step 1: Stop the currently running container

```console
$ docker stop redmine
```

Or using Docker Compose:

```console
$ docker-compose stop redmine
```

#### Step 2: Run the backup command

We need to mount two volumes in a container we will use to create the backup: a directory on your host to store the backup in, and the volumes from the container we just stopped so we can access the data.

```console
$ docker run --rm -v /path/to/redmine-backups:/backups --volumes-from redmine busybox \
  cp -a /bitnami/redmine /backups/latest
```

### Restoring a backup

Restoring a backup is as simple as mounting the backup as volumes in the containers.

For the MariaDB database container:

```diff
 $ docker run -d --name mariadb \
   ...
-  --volume /path/to/mariadb-persistence:/bitnami/mariadb \
+  --volume /path/to/mariadb-backups/latest:/bitnami/mariadb \
   bitnami/mariadb:latest
```

For the Redmine container:

```diff
 $ docker run -d --name redmine \
   ...
-  --volume /path/to/redmine-persistence:/bitnami/redmine \
+  --volume /path/to/redmine-backups/latest:/bitnami/redmine \
   bitnami/redmine:latest
```
