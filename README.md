**This build resembles the dockerfile/nginx build with the following changes**

* We utilize a run script that checks for ssl cert/keys provided via env variables
* This run script also allows for setting up a ramcache directory of specified size. This is meant for nginx proxy_cache so use it wisely

## Nginx Dockerfile

This repository contains **Dockerfile** of [Nginx](http://nginx.org/) for [Docker](https://www.docker.com/).


### Base Docker Image

* [dockerfile/ubuntu](http://dockerfile.github.io/#/ubuntu)


### Installation

1. Install [Docker](https://www.docker.com/).

2. Download [automated build](https://registry.hub.docker.com/u/inanimate/nginx-ssl/) from public [Docker Hub Registry](https://registry.hub.docker.com/): `docker pull inanimate/nginx-ssl`

   (alternatively, you can build an image from Dockerfile: `docker build -t="inanimate/nginx-ssl" github.com/inanimate/nginx-ssl`)


### Usage

    docker run -d -p 80:80 inanimate/nginx-ssl

#### Attach persistent/shared directories

    docker run -d -p 80:80 -v <sites-enabled-dir>:/etc/nginx/sites-enabled -v <certs-dir>:/etc/nginx/ssl -v <log-dir>:/var/log/nginx -v <html-dir>:/var/www/html inanimate/nginx-ssl

After few seconds, open `http://<host>` to see the welcome page.

#### Utilizing ramcache for your nginx proxy cache

    docker run -d -p 80:80 -e RAMCACHE_LOC="/mnt" -e RAMCACHE_SIZE="100M" inanimate/nginx-ssl

* Note that you will need to enable proxy cache in your nginx configuration for your site and then point to the directory specific to `RAMCACHE_LOC`
* `RAMCACHE_SIZE` needs to be a value acceptable to the `size=` parameter to the `mount` command!

This will create a ramcache of `100MiB` in RAM and mount it to `/mnt`. Then you can specify /mnt as the location to store cache in nginx!

#### Send your ssl key/cert over vars

Let make note of the imporant vars here and what they mean to nginx. If you aren't sure on what is what, check [here](https://gist.github.com/bradmontgomery/6487319) for a good tutorial.

* `SSL_KEY` - *ssl_certificate_key*
* `SSL_CERT` - *ssl_certificate*
* `SSL_TRUST_CERT` - *ssl_trusted_certificate*
