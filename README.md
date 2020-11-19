This repository will guide you to deploy a **SOLID Data Station** for the **Personal Health Train** project using [Docker](https://www.docker.com/).

Start by cloning this repository on your server with port 80 and 443 publicly available

```bash
git clone https://github.com/MaastrichtU-IDS/solid-station.git
cd solid-station
```

> We are using **solid.semanticscience.org** as URL for the SOLID pods in this example, change it for your URL.

## Deploy nginx proxy

We will deploy a [nginx proxy](https://github.com/nginx-proxy/) with HTTPS support using Docker on port 80 and 443 of our server to redirect all subdomains call to the SOLID server we will start.

```bash
cd nginx-proxy
docker-compose up -d
```

Check the logs with:

```bash
docker-compose logs
```

> N.B. you can also try to use the proxy of your choice as explained in the [official SOLID documentation](https://solidproject.org//self-hosting/nss).

Go back to the root of the git repository: `cd ..`

## Deploy the SOLID server

Change the `docker-compose.yml` to set the right path for HTTPS certificates in `VIRTUAL_HOST`, `LETSENCRYPT_HOST` variables (wildcards are not supported.

1. Create the following directories for the SOLID pods data on your server and set their owner to `1000`

```bash
sudo mkdir -p /data/solid/config
sudo mkdir -p /data/solid/data
sudo chown -R 1000:1000 /data/solid
```

2. If this the first time you start the SOLID server, then it will fail to start due to the HTTPS certificates that have not been generated already. You need to first run a dummy nginx container, and go to the URL you want for your SOLID server to trigger LetsEncrypt to generate the HTTPS certificates for it (change `solid.semanticscience.org` to your address in this example):

```bash
docker run --rm --name nginx -e VIRTUAL_HOST=solid.semanticscience.org -e LETSENCRYPT_HOST=solid.semanticscience.org nginx
```

3. You can now stop this dummy container and start the SOLID server with `docker-compose`!

```bash
docker-compose up -d
```

> See also the [official SOLID server Docker documentation](https://github.com/solid/node-solid-server/tree/master/docker-image).