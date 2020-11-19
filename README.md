This repository will guide you to deploy a **SOLID Data Station** for the **Personal Health Train** project using [Docker](https://www.docker.com/).

See Solid server Docker documentation: https://github.com/solid/node-solid-server/tree/master/docker-image

```bash
git clone https://github.com/MaastrichtU-IDS/solid-station.git
cd solid-station
```

> We are using **solid.semanticscience.org** as URL for the SOLID pods in this example, change it for your URL.

## Deploy nginx proxy

We will deploy a nginx-proxy on port 80 and 443 of our server to redirect all subdomains call to the started SOLID pod

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

## Deploy SOLID pods

Change the `docker-compose.yml` to set the right path for HTTPS certificates in `VIRTUAL_HOST`, `LETSENCRYPT_HOST` variables (wildcards are not supported.

1. Create the following directories for the SOLID pods data on your server and set their owner to `1000`

```bash
sudo mkdir -p /data/solid/config
sudo mkdir -p /data/solid/data
sudo chown -R 1000:1000 /data/solid
```

2. Run docker-compose to start the SOLID pod:

```bash
docker-compose up -d
```

3. If SOLID fails to start it might be due to the HTTPS has not been generated already you need to try a dummy nginx and go to the URL of your pod to trigger LetsEncrypt to generate the HTTPS certificates for it (change `solid.semanticscience.org` to your address in this example):

```bash
docker run --rm --name nginx -e VIRTUAL_HOST=solid.semanticscience.org -e LETSENCRYPT_HOST=solid.semanticscience.org nginx
```

> You can now stop this dummy container and start SOLID.

