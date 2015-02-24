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

    docker run -d -p 80:80 --privileged -e RAMCACHE_LOC="/mnt" -e RAMCACHE_SIZE="100M" inanimate/nginx-ssl

* Note that you will need to enable proxy cache in your nginx configuration for your site and then point to the directory specific to `RAMCACHE_LOC`
* `RAMCACHE_SIZE` needs to be a value acceptable to the `size=` parameter to the `mount` command!

This will create a ramcache of `100MiB` in RAM and mount it to `/mnt`. Then you can specify /mnt as the location to store cache in nginx!

#### Sending your ssl key/cert over vars

Let make note of the imporant vars here and what they mean to nginx. If you aren't sure on what is what, check [here](https://gist.github.com/bradmontgomery/6487319) for a good tutorial or see how I [did](https://gist.github.com/InAnimaTe/2e2b1058af02d3ac2033) it.

* `SSL_KEY` - *ssl_certificate_key*
* `SSL_CERT` - *ssl_certificate*
* `SSL_TRUST_CERT` - *ssl_trusted_certificate*

Obviously, the way you use variables is up to your site configuration in nginx. You may not even need the trusted cert unless you enable ssl stapling.

Initially, I tried to just add these keys in their newline form to my crane.yml (more on crane [here](https://github.com/michaelsauter/crane)). This didn't work:(
I then tested with the `docker run` command specifying them as `SSL_CERT="<cert with newlines>"` which yielded good results! After dabbling around with yaml syntax for handling multi-line portions of text, I decided I was just going to have to flatten out the ssl stuff to a single line string. This meant replacing all the newlines with literal `\n`'s.

So here are the two ways to conquer this via `docker run`

* `{snip..}` is just where I cut extra for this example.

```bash
docker run -d -p 80:80 -e SSL_CERT="-----BEGIN CERTIFICATE-----\nMIIGSzCCBTOgAwIBAgI{snip..}" inanimate/nginx-ssl
```

You can also do multiline on your prompt:

```bash
docker run -d -p 80:80 -e SSL_CERT="-----BEGIN CERTIFICATE-----
MIIGSzCCBTOgAwIBAgI{snip..}" inanimate/nginx-ssl
```

Either way you do, atm, there is one sad flaw: nsenter doesn't support entering a namespace which has environment variables that contain spaces. There is already an [issue](https://github.com/jpetazzo/nsenter/pull/52#issuecomment-72889442) existent on this so hopefully it gets fixed one day:)
