# webhook.site Docker

This is a Dockerfile for [webhook.site](https://github.com/fredsted/webhook.site), a great tool for easily testing webhooks.

## Building

To build it, run the following command:

	$ docker build --tag dahyphenn/webhook.site .

Optionally, you can build the image with the UID and GID of the host user (only required if you want to share volumes or have other permissions issues):

	$ docker build --build-arg UID=$(id -u) --build-arg GID=$(id -g) --tag dahyphenn/webhook.site . 

## Running

To run the container, run a command like the following:

	$ docker run -it --rm --init -p 80:80 --name webhook_site dahyphenn/webhook.site

## Pusher

To take full advantage of webhook.site, you need to include [pusher.com](https://www.pusher.com) credentials in the apllications `.env` file. To do this, create a copy of `.env.example` and add the Pusher details to it. Then, when you next run include the `.env` like so:

	  $ docker run -it --rm --init -p 80:80 -v $(pwd)/.env:/opt/app/.env --name webhook_site dahyphenn/webhook.site

## Persistent data

To have persistent data, you can do one of the following:

1. Link the existing SQLite database via Docker volumes
2. Add a MySQL or Postgresql database container and include the details in the `.env` file e.g.:

	DB_CONNECTION
	DB_HOST
	DB_PORT
	DB_DATABASE
	DB_USERNAME
	DB_PASSWORD

Note after changing the above, you will need to re-run the migration as follows:

	$ docker exec -it webhook_site sh
	$ cd /opt/app
	$ php artisan migrate

## Credits

This Dockerfile was heavily inspired by [https://github.com/chrootLogin/docker-nextcloud](github.com/chrootLogin/docker-nextcloud).