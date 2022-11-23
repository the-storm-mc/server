# Server

This repository is responsible for configuring and deploying all of the software necessary for running The Storm, including websites and Minecraft servers.

## Requirements

-   [Earthly](https://earthly.dev/)
-   [Docker](https://www.docker.com/)

You'll need some secrets set with Earthly secrets:

-   `sj/mkdocs_gh_token`: A GitHub token that has permisions to pull [squidfunk/mkdocs-material-insiders](https://github.com/squidfunk/mkdocs-material-insiders)
-   `sj/aws_access_key_id`: An AWS access key ID with Route53 permissions
-   `sj/aws_secret_access_key`: An AWS secret access key with Route54 permissions
-   `sj/datadog_api_key`: A Datadog API key

## Usage

You'll need a copy of the following repositories all in the same directory:

```bash
> tree -L 1 .
.
├── creative
├── docs
├── server
├── site
└── survival
```

Synchronize with production: `earthly +sync`.

Deploy: `earthly +up`.
