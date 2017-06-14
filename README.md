# webhook.site Docker

> Easily test HTTP webhooks with this handy tool that displays requests in realtime.

This is a Dockerfile for [webhook.site](https://github.com/fredsted/webhook.site), an API and frontend to test your webhooks. Built on the PHP FPM 7.1 Alpine Linux image.

## Build it

To build it, run the following command:

    $ docker build --tag dahyphenn/webhook.site .

Additionally, you can build the image with the UID and GID of the host user is required. This is only needed if you want to share volumes and/or have permissions issues:

    $ docker build --build-arg UID=$(id -u) --build-arg GID=$(id -g) --tag dahyphenn/webhook.site . 

## Run it

Here is an example to run the container:

    $ docker run -it --rm --init -p 80:80 --name webhook_site dahyphenn/webhook.site

## Add Pusher

To take full advantage of webhook.site, you need to include [pusher.com](https://www.pusher.com) credentials in the apllications `.env` file. To do this, create a copy of `.env.example` and add your Pusher API details to it. Then, when you next run include the `.env` like so:

      $ docker run -it --rm --init -p 80:80 -v $(pwd)/.env:/opt/app/.env --name webhook_site dahyphenn/webhook.site

You should now be able to see live results without the need to refresh.

## Persistent data

To have persistent data, you can do one of the following:

1. Link the existing SQLite database via Docker volumes
2. Link a MySQL or Postgresql database container instance and include connection details in the `.env` file

Example of including databsae `env` variables:

    DB_CONNECTION
    DB_HOST
    DB_PORT
    DB_DATABASE
    DB_USERNAME
    DB_PASSWORD

Note after changing the above, you will need to re-run the migration once as follows:

    $ docker exec -it webhook_site sh
    $ cd /opt/app
    $ php artisan migrate

## Docker compose example:

Here is a `docker-compose.yml` example, which uses a persistent database, and links to a custom `.env` file:

    version: '3'
    services:
      webhook-site-app:
        image: dahyphenn/webhook.site
        links:
          - webhook-site-db
        depends_on:
          - webhook-site-db
        volumes:
          - ./.env:/opt/app/.env
        ports:
          - "80:80"
      webhook-site-db:
        image: mysql
        environment:
          - MYSQL_ROOT_PASSWORD=<ADD_PASSWORD>
          - MYSQL_DATABASE=app
          - MYSQL_USER=app
          - MYSQL_PASSWORD=<PASSWORD>
        volumes:
          - webhook-site-db-data-volume:/var/lib/mysql
    volumes:
      webhook-site-db-data-volume:

You would need to include a `.env` file with the database details like the following example:

    APP_ENV=local
    APP_DEBUG=true
    APP_KEY=
    APP_URL=http://localhost

    # Set this for Pusher live reload functionality
    PUSHER_KEY=
    PUSHER_SECRET=
    PUSHER_APP_ID=
    PUSHER_CLUSTER=

    DB_CONNECTION=mysql
    DB_HOST=webhook-site-db
    DB_PORT=3306
    DB_DATABASE=app
    DB_USERNAME=app
    DB_PASSWORD=<PASSWORD>

You should be able to see the app running in your browser at [http://localhost](http://localhost).

## Credits

Takes inspiration from [https://github.com/chrootLogin/docker-nextcloud](github.com/chrootLogin/docker-nextcloud).
