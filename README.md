# Server

This repository is responsible for configuring and deploying all of the software necessary for running The Storm's Minecraft servers.

## Requirements

All of the requirements of the other repos must be met. In addition, this repository directly requires the following:

-   [Earthly](https://earthly.dev/)
-   [Docker](https://www.docker.com/)

You'll need some secrets set with Earthly secrets:

-   `sj/aws_access_key_id`: An AWS access key ID with Route53 permissions
-   `sj/aws_secret_access_key`: An AWS secret access key with Route54 permissions
-   `sj/datadog_api_key`: A Datadog API key

## Usage

You'll need a copy of the following repositories all in the same directory:

```bash
> tree -L 1 .
.
├── creative
├── server
└── survival
```

Synchronize with production: `earthly +sync`.

Deploy: `earthly +up`.
