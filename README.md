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
      dockerfile: Dockerfile-dart-pad
    image: liudonghua123/dart-pad
    networks:
      - nginx-proxy
    ports:
      - "8000"
    environment:
      - VIRTUAL_HOST=<dart-pad-front-end-host-or-domain>
    entrypoint: bash -c "cd /dart-pad && DARTPAD_BACKEND=<http://dart-pad-back-end-host-or-domain> grind serve-custom-backend"

  dart-services:
    build:
      context: .
      dockerfile: Dockerfile-dart-services
    image: liudonghua123/dart-services
    networks:
      - nginx-proxy
    ports:
      - "8082"
    volumes:
      - ./dart-services:/dart-services
    environment:
      - VIRTUAL_HOST=<dart-pad-back-end-host-or-domain>\

networks:
  nginx-proxy:
    external:
      name: nginx-proxy-default

```

## Next work

- [x] Add README.md for documentation.
- [ ] Rewrite to only expose ONE endpoint.

## License

MIT License

Copyright (c) 2021 liudonghua