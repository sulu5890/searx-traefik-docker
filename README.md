# searx-traefik-docker

A simple way to create a searx instance, using traefik. To use this you do need a domain. This is currently not set up for a local server.

## What is included ?

| Name | Description | Docker image | Dockerfile |
| -- | -- | -- | -- |
| [Traefik](https://github.com/traefik/traefik) | The Cloud Native Application Proxy (create a LetsEncrypt certificate automatically) | [traefik:v2.3](https://hub.docker.com/_/traefik) | [Dockerfile](https://github.com/traefik/traefik-library-image/blob/6827292e34bb173568e72f20281897946d635e4a/alpine/Dockerfile) |
| [Filtron](https://github.com/asciimoo/filtron) |  Filtering reverse HTTP proxy, bot and abuse protection | [dalf/filtron:latest](https://hub.docker.com/r/dalf/filtron) | See [asciimoo/filtron#4](https://github.com/asciimoo/filtron/pull/4) |
| [Searx](https://github.com/asciimoo/searx) | searx by itself | [searx/searx:latest](https://hub.docker.com/r/searx/searx) | [Dockerfile](https://github.com/searx/searx/blob/master/Dockerfile) |
| [Morty](https://github.com/asciimoo/morty) | Privacy aware web content sanitizer proxy as a service. | [dalf/morty:latest](https://hub.docker.com/r/dalf/morty) | [Dockerfile](https://github.com/dalf/morty/blob/master/Dockerfile) |

## How to use it
- [Install docker](https://docs.docker.com/install/)
- [Install docker-compose](https://docs.docker.com/compose/install/) (be sure that docker-compose version is at least 1.9.0).
- only on MacOSX: ```brew install coreutils``` to install ```greadlink```
- Get searx-traefik-docker
```sh
cd /usr/local
git clone https://github.com/sulu5890/searx-traefik-docker.git
cd searx-traefik-docker
```
- Edit the [.env](https://github.com/sulu5890/searx-traefik-docker/blob/master/.env) file according to your need. **Ensure you replace your domain and email**.
- Check everything is working: ```./start.sh```,
- ```cp searx-docker.service.template searx-docker.service```
- edit the content of ```WorkingDirectory``` in the ```searx-traefik-docker.service``` file (only if the installation path is different from /usr/local/searx-traefik-docker)
- Install the systemd unit :
```sh
systemctl enable $(pwd)/searx-traefik-docker.service
systemctl start searx-traefik-docker.service
```

## Note on the image proxy feature

The searx image proxy is activated by default using [Morty](https://github.com/asciimoo/morty).

The default [Content-Security-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy) allow the browser to access to {SEARX_HOSTNAME} and ```https://*.tile.openstreetmap.org;```.

If some users wants to disable the image proxy, you have to modify [docker-compose.yaml](https://github.com/sulu5890/searx-traefik-docker/blob/master/docker-compose.yaml). Replace the ```img-src 'self' data: https://*.tile.openstreetmap.org;``` by ```img-src * data:;```

Keep in mind that it is best practice to leave the proxy enabled.

## Custom docker-compose.yaml

Do not modify docker-compose.yaml otherwise you won't be able to update easily from the git repository.

It is possible to the [extend feature](https://docs.docker.com/compose/extends/) of docker-compose :
- stop the service : ```systemctl stop searx-traefik-docker.service```
- create a new docker-compose-extend.yaml, check with ```start.sh```
- update searx-traefik-docker.service (see SEARX_DOCKERCOMPOSEFILE)
- restart the servie  : ```systemctl restart searx-traefik-docker.service```

## Multi Architecture Docker images

For now only the amd64 platform is supported.

## How to update ?

Check the content of [```update.sh```](https://github.com/searx/searx-docker/blob/master/update.sh)

## Access to the Filtron API

The [Filtron API](https://github.com/asciimoo/filtron#api) is available on ```http://localhost:4041```. This is only available to your local machine, and will not be accessible to the broader internet.

For example, to display the loaded rules:
```
curl http://localhost:4041/rules | jq
```

## Credits

Please give most of the thanks for this to the official [searx-docker](https://github.com/searx/searx-docker) that makes use of the caddy proxy. This is a modified version of that, changed to use traefik instead. Please report any bugs or issues here, and not to them.
