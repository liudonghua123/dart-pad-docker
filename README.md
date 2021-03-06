# dart-pad-docker

## What is it

This is a simple docker project which build a own dart-pad services.

## How to use it

The quick way to start up is using [nginx-proxy](https://github.com/nginx-proxy/nginx-proxy) with [docker-compose](https://docs.docker.com/compose). You need to expose two endpont: one for the front-end, one for the back-end. So replace `<...>` in the following `docker-compose.yml`.

```yaml
version: "3.7"

services:
  nginx-proxy:
    image: jwilder/nginx-proxy
    ports:
      - "80:80"
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro

  dart-pad:
    build:
      context: .
      dockerfile: dockerfiles/Dockerfile-dart-pad
    image: liudonghua123/dart-pad
    networks:
      - nginx-proxy
    environment:
      - VIRTUAL_HOST=<dart-pad-front-end-host-or-domain>
    entrypoint: bash -c "cd /dart-pad && DARTPAD_BACKEND=<http://dart-pad-back-end-host-or-domain> grind serve-custom-backend"

  dart-services:
    build:
      context: .
      dockerfile: dockerfiles/Dockerfile-dart-services
    image: liudonghua123/dart-services
    networks:
      - nginx-proxy
    environment:
      - VIRTUAL_HOST=<dart-pad-back-end-host-or-domain>

networks:
  nginx-proxy:
    external:
      name: nginx-proxy-default

```

**Updated: You can use standalone version of dart-pad NOW, see `docker-compose-standalone.yml.sample` for more details. In this way, you can only expose ONE endpoint which is `<dart-pad-host-or-domain>` in the following `docker-compose.yml`.**

```yaml
version: "3.7"
services:
  nginx-proxy:
    image: jwilder/nginx-proxy
    ports:
      - "80:80"
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro

  dart-pad-standalone:
    build:
      context: .
      dockerfile: dockerfiles/Dockerfile-dart-pad-standalone
    image: liudonghua123/dart-pad-standalone
    environment:
      - VIRTUAL_HOST=<dart-pad-host-or-domain>
    depends_on:
      - dart-services
    networks:
      - nginx-proxy

  dart-services:
    image: liudonghua123/dart-services
    networks:
      - nginx-proxy

networks:
  nginx-proxy:
    external:
      name: nginx-proxy-default
```

**Updated: You can use extended version of dart-services which support customize the default installed packages for dart/flutter NOW. Just ONLY replace `liudonghua123/dart-services` with `liudonghua123/dart-services-extended`. You can see `dockerfiles/Dockerfile-services-extended` how I did it. And you can modified the your customized default packages according to [make flutter example change](https://github.com/liudonghua123/dart-pad-docker/commit/b6f562a6dadf106292adedf3e1c6b2df7e24bb7c) commit.**

![snapshots](snapshots.png)

## Next work

- [x] Add README.md for documentation.
- [x] Rewrite to only expose ONE endpoint.
- [x] Customize the default installed packages for dart/flutter.
- [ ] Improve the customization default packages process.

## License

MIT License

Copyright (c) 2021 liudonghua
