**This build resembles the dockerfile/nginx build with one small change: we utilize a run script that checks for ssl cert/keys provided via env variables:)**

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

#### Send your ssl key/cert over vars

    docs to come...
