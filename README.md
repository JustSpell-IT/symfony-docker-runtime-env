## Minimal Docker Runtime LEPP Environment for Symfony

This Docker environment is designed to provide a robust and convenient starting point for Symfony application development. The Docker services included are PHP, NGINX, Varnish, Adminer, PostgreSQL, and H2-Proxy.

## Table of Contents
1. [See it working!](#see-it-working)
2. [Environment Architecture](#environment-architecture)
3. [Quick Start](#quick-start)
4. [Configure Xdebug](#configure-xdebug)
5. [Directory Structure](#directory-structure)
6. [Database Connection and Setup](#setting-up-database-connection)
7. [Additional Notes](#additional-notes)

---

## See it working! <a name="see-it-working"></a>

To see the Docker environment in action, we have included the Symfony Demo project in the `symfony` directory. The [Symfony Demo](https://github.com/symfony/demo.git
) project demonstrates the environment's capabilities and the basic structure of a Symfony application.

## Environment Architecture <a name="environment-architecture"></a>

The Docker environment for Symfony currently supports PostgreSQL databases. It consists of six Docker services, all optimized for a Symfony application.

## Quick Start <a name="quick-start"></a>

Follow these steps to get your Docker environment up and running:

### Clone the repository: 

```shell
$ git clone https://github.com/soft-cloud-dev/symfony-docker-runtime-env.git
```

### Navigate into the directory: 

```shell
$ cd symfony-4-docker-runtime-env
```

### Confiture database connection:

```bash
$ ./db-setup.sh
```

### Start the Docker environment: 

```shell
$ docker-compose up
```
**Make sure to run the** `docker-compose up` **command from the root directory of the project.** This ensures that the necessary environment variables are read correctly.

For debugging make sure to adjust the configuration as needed for `XDEBUG_CONFIG` and `PHP_IDE_CONFIG` environment variables in the `docker-compose.yml` file.

For debugging, you may need to add a server for PHP, as explained in the [Add a debug server](https://www.jetbrains.com/help/phpstorm/configuring-xdebug.html) section of JetBrains documentation.

## Configure Xdebug <a name="configure-xdebug"></a>

To configure Xdebug, navigate to the `docker-compose.yml` file and adjust the configuration as needed for the `XDEBUG_CONFIG` and `PHP_IDE_CONFIG` environment variables.

## Directory Structure <a name="directory-structure"></a>

Here's the structure of the repository:

```
.
├── h2-proxy
│   └── conf.d
└── symfony
    ├── assets
    ├── bin
    ├── config
    ├── data
    ├── docker
    │   ├── init.sql
    │   ├── nginx
    │   ├── pgsql
    │   ├── php
    │   └── varnish
    ├── migrations
    ├── public
    ├── src
    ├── templates
    ├── tests
    ├── translations
    ├── var
    └── vendor
```

Your Symfony application's source files will be in the `src` directory under `symfony`. Docker configurations for nginx and PHP can be found in the `docker` directory.

And let's update the corresponding section in the documentation:

## Setting Up Database Connection <a name="setting-up-database-connection"></a>

Before starting your application, you will need to set up the database connection. The `DATABASE_URL` for the database connection is stored in the `./symfony/.env.local` file. This file is created by the `db-setup.sh` script which loads the environment variables from the `.env` file in your project root and substitutes them into the `DATABASE_URL` setting.

The `db-setup.sh` script will also update the `./symfony/docker/pgsql/init.sql` file with the correct database, user, and password for your PostgreSQL service. This ensures that the database is set up correctly when the Docker environment is started.

The `.env.local` file is already included in the `.gitignore` file, so it won't be tracked by Git.

**To set up the database connection:**

1. Open the `.env` file in the root of your project.
2. Set the `POSTGRES_DB`, `POSTGRES_USER`, and `POSTGRES_PASSWORD` variables to your database name, database user, and database password, respectively.
3. Run the `db-setup.sh` script:

```bash
$ ./db-setup.sh
```

This script will create the `./symfony/.env.local` file with the correct `DATABASE_URL` setting and update the `./symfony/docker/pgsql/init.sql` file with your database settings. If you need to change your database connection in the future, simply update the `.env` file and rerun the `db-setup.sh` script.

The `./symfony/.env.local` file should now contain a line that looks like this:

```
DATABASE_URL="postgresql://your-db-user:your-db-password@127.0.0.1:5432/your-db-name?serverVersion=13&charset=utf8"
```

Where `your-db-user`, `your-db-password`, and `your-db-name` are the values you set in the `.env` file.

The `./symfony/docker/pgsql/init.sql` file should now contain the following SQL commands:

```sql
CREATE USER your-db-user WITH PASSWORD 'your-db-password';
CREATE DATABASE your-db-name;
GRANT ALL PRIVILEGES ON DATABASE your-db-name TO your-db-user;
```

Where `your-db-user`, `your-db-password`, and `your-db-name` are the values you set in the `.env` file.

When Docker is started, it will use the `init.sql` script to set up the PostgreSQL service. This will create the user, database, and assign the necessary privileges. The `volumes` setting in your `docker-compose.yml` file should look like this:

```yaml
volumes:
  - db-data:/var/lib/postgresql/data:rw
  - ./symfony/docker/pgsql/init.sql:/docker-entrypoint-initdb.d/init.sql:ro
```

This configuration ensures that the `init.sql` script is run when the PostgreSQL container is started, solving the problem of the "Role 'sf_user' does not exist" error.

## Additional Notes <a name="additional-notes"></a>

### Varnish Cache <a name="varnish-cache"></a>

Varnish Cache is a powerful, open-source, HTTP engine/reverse HTTP proxy. It serves as an HTTP accelerator and is designed to help you improve your website's performance. In this Docker environment, Varnish is set up to work with your Symfony application out of the box.

### H2-Proxy <a name="h2-proxy"></a>

H2-Proxy is a proxy built for HTTP/2 support. It's included in the Docker environment but it should not be used in the production setting. In production, consider using a robust solution like Nginx or HAProxy with full HTTP/2 support.

### Updating the Environment <a name="updating-environment"></a>

Keep your Docker environment up-to-date by pulling the latest changes from the [repository](https://github.com/soft-cloud-dev/symfony-docker-runtime-env.git). It's important to keep your environment updated, as we periodically make improvements and fix issues.

```shell
$ git pull origin main
```

### Customizing the Environment <a name="customizing-environment"></a>

While the environment comes pre-configured, you can customize it to suit your needs.

 All the Dockerfiles and configuration files are available in the repository. You can modify these files to tweak the environment. Make sure to rebuild your Docker images after making any changes to the Dockerfiles.

```shell
$ docker-compose build
```

Please, ensure to review your changes thoroughly and understand the consequences before pushing them into a live environment.

This Docker environment for Symfony is designed to provide a solid foundation for your Symfony application development. It includes the essential services required for a typical Symfony application. Happy coding!

## License

This project is licensed under the MIT License.

## Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

