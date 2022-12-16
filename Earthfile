VERSION 0.6
FROM ubuntu:jammy
WORKDIR /workspace
ARG --required stage

gomplate:
  FROM golang:alpine
  WORKDIR /workspace
  RUN go install github.com/hairyhenderson/gomplate/v3/cmd/gomplate@v3.11.3

render.docker-compose:
  FROM +gomplate
  COPY docker-compose.yml.tmpl .
  RUN --secret=BORG_PASSPHRASE=+secrets/user/tsmc/borg_passphrase --secret=DATADOG_API_KEY=+secrets/user/tsmc/datadog_api_key --secret=AWS_ACCESS_KEY_ID=+secrets/user/tsmc/aws_access_key_id --secret=AWS_SECRET_ACCESS_KEY=+secrets/user/tsmc/aws_secret_access_key gomplate -f docker-compose.yml.tmpl -o docker-compose.yml
  SAVE ARTIFACT docker-compose.yml

build:
  COPY +render.docker-compose/ .
  COPY config config/
  COPY ../survival+build/ servers/survival/$stage
  COPY ../creative+build/ servers/creative/$stage
  SAVE ARTIFACT ./*

rsync:
  FROM alpine:3
  RUN apk add rsync openssh
  RUN mkdir -p /root/.ssh/
  COPY known_hosts /root/.ssh/known_hosts

sync:
  FROM +rsync
  ARG --required stage
  WORKDIR /workspace
  COPY (+build/ --stage=$stage) .
  RUN --no-cache --ssh rsync --progress -a --checksum * debian@minecraft.ts-mc.net:/home/debian

up:
  ARG --required stage
  FROM +sync --stage=$stage
  RUN --no-cache --ssh ssh debian@minecraft.ts-mc.net "cd /home/debian && docker-compose pull && docker-compose build && docker-compose down --remove-orphans && docker-compose up -d --remove-orphans"
