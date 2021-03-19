# Docker Image for Grav to deploy in Heureka

The Dockerfile and the Readme.md bases on the official docker image for Grav.

This currently is pretty minimal and uses:

* apache-2.4.38
* GD library
* Unzip library
* php7.4
* php7.4-opcache
* php7.4-acpu
* php7.4-yaml
* cron
* vim editor

## Persisting data

To save the Grav site data to the host file system (so that it persists even after the container has been removed), simply map the container's `/var/www/html` directory to a named Docker volume or to a directory on the host.

> If the mapped directory or named volume is empty, it will be automatically populated with a fresh install of Grav the first time that the container starts. However, once the directory/volume has been populated, the data will persist and will not be overwritten the next time the container starts.

## Building the image from Dockerfile

```
docker build -t soteto/grav:latest .
docker push soteto/grav
```

## Running Grav Image with Latest Grav + Admin:

```
docker run -p 8000:80 soteto/grav:latest
```

Point browser to `http://localhost:8000/docu` and create user account...

## Running Grav Image with Latest Grav + Admin with a named volume (can be used in production)

```
docker run -d -p 8000:80 --restart always -v [...see bottom...] grav:latest
```

## Running Grav Image with docker-compose and a volume mapped to a local directory

Running `docker-compose up -d` with the following docker-compose configuration will automatically build the Grav image (if the Dockerfile is in the same directory as the docker-compose.yml file). Then the Grav container will be started with all of the site data persisted to a named volume (stored in the `./grav` directory.

```.yml
volumes:
  grav-accounts:
    driver: local
    driver_opts:
      type: none
      device: $PWD/grav/accounts
      o: bind
  grav-images:
    driver: local
    driver_opts:
      type: none
      device: $PWD/grav/images
      o: bind
  grav-languages:
    driver: local
    driver_opts:
      type: none
      device: $PWD/grav/languages
      o: bind
  grav-pages:
    driver: local
    driver_opts:
      type: none
      device: $PWD/grav/pages
      o: bind
  grav-plugins:
    driver: local
    driver_opts:
      type: none
      device: $PWD/grav/plugins
      o: bind
  grav-themes:
    driver: local
    driver_opts:
      type: none
      device: $PWD/grav/themes
      o: bind
  grav-config:
    driver: local
    driver_opts:
      type: none
      device: $PWD/grav/config
      o: bind

services:
  grav:
    build: ./
    ports:
      - 8080:80
    volumes:
      - grav-accounts:/var/www/html/docu/user/accounts
      - grav-images:/var/www/html/docu/user/images
      - grav-languages:/var/www/html/docu/user/languages
      - grav-pages:/var/www/html/docu/user/pages
      - grav-plugins:/var/www/html/docu/user/plugins
      - grav-themes:/var/www/html/docu/user/themes
      - grav-config:/var/www/html/docu/user/config
```
