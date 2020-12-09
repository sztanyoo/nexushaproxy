# nginx-reverseproxy-nexus

This is a simple nexus repository with a reverse proxy in front of it.
Nexus can be used for any repository types, even Docker registry works like a charm.

Traffic between Nexus and Nginx is on http, while Nginx exposes https, too

# Usage

## 1. Certificates

First you'll need valid certificates to be placed in ssl/live/SERVER.DOMAIN.TLD/

The easiest way to do it is using certbot:

```
cd ssl
certbot certonly --manual  --preferred-challenges dns -w . -d SERVER.DOMAIN.TLD --work-dir . --logs-dir . --config-dir .
<< follow the instructions >>
cd ..
```
Then update nginx.conf to use the certificates from the right path:

- ssl_certificate
- ssl_certificate_key
- ssl_trusted_certificate

## 2. Configure & Start service

One might want to configure the public listener ports for nginx, this can be done via docker-compose.yml:

```
ports:
  - "<< desired http port >>:80"
  - "<< desired https port >>:443"
```

Replace SERVER.DOMAIN.TLD in `nginx.conf` to the fqdn of your machine.

The following command will do the heavy lifting:

```
docker-compose up -d
```

## 3. Create repositories

By default the nexus service is available on https://SERVER.DOMAIN.TLD/nexus

You can log in using username `admin` and the default password can be retrieved using the following cli: `docker-compose exec nexus cat /nexus-data/admin.password`.

One might want to create a Docker registry, eg. `netbank`.

## 4. Start using the Docker registry

On your dev/whatever machine login to the registry: `docker login https://SERVER.DOMAIN.TLD/repository/netbank/`.

Tag a locally available image: `docker tag 37d543c9063d2 SERVER.DOMAIN.TLD/netbank/myapplication:latest`.

Push the image to the registry: `docker push SERVER.DOMAIN.TLD/netbank/myapplication:latest`.

