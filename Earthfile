VERSION 0.6
FROM ubuntu:jammy
WORKDIR /workspace

gomplate:
  FROM golang:alpine
  WORKDIR /workspace
  RUN go install github.com/hairyhenderson/gomplate/v3/cmd/gomplate@v3.11.3

render.docker-compose:
  FROM +gomplate
  COPY docker-compose.yml.tmpl .
  RUN --secret=BORG_TSMC_SURVIVAL=+secrets/sj/borgmatic/tsmc/survival --secret=DATADOG_API_KEY=+secrets/sj/datadog_api_key --secret=AWS_ACCESS_KEY_ID=+secrets/sj/aws_access_key_id --secret=AWS_SECRET_ACCESS_KEY=+secrets/sj/aws_secret_access_key gomplate -f docker-compose.yml.tmpl -o docker-compose.yml
  SAVE ARTIFACT docker-compose.yml

build:
  COPY +render.docker-compose/ .
  COPY config config/
  COPY ../survival+build/ servers/survival/
  COPY ../creative+build/ servers/creative/
  SAVE ARTIFACT ./*

rsync:
  FROM alpine:3
  RUN apk add rsync openssh
  RUN mkdir -p /root/.ssh/
  COPY known_hosts /root/.ssh/known_hosts

sync:
  FROM +rsync
  WORKDIR /workspace
  COPY +build/ .
  RUN --no-cache --ssh rsync --progress -a --checksum * debian@ts-mc.net:/home/debian

up:
  FROM +sync
  RUN --no-cache --ssh ssh debian@ts-mc.net "cd /home/debian && docker-compose pull && docker-compose build && docker-compose down --remove-orphans && docker-compose up -d --remove-orphans"
