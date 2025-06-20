# Objectstore Download • GitHub Action

Easily pull artefacts from the **DownToZero.cloud Objectstore** into your GitHub Actions workflow.
This composite action wraps a single `curl` call so you can keep pipelines declarative and secure.

## Prerequisites

* A DownToZero **Objectstore API key** with `read` permission stored as an [encrypted secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets) (e.g. `DTZ_API_KEY`).
* The object you want to download must already exist in the bucket configured for your key.

## Inputs

| Name         | Required | Description                                                                                                            | Default |
| ------------ | :------: | ---------------------------------------------------------------------------------------------------------------------- | ------- |
| `api_key`    |  **Yes** | Objectstore API key used for authentication.                                                                           | –       |
| `name`       |  **Yes** | Local filename to write the artefact to. When `object_key` is omitted this is **also** used as the key in Objectstore. | –       |
| `object_key` |    No    | Remote object key in Objectstore. If left blank the value of `name` is used instead.                                   | `""`    |

## Usage

### Basic example

Download an artefact whose key is the same as the desired filename:

```yaml
- name: Download build artefact
  uses: DownToZero-Cloud/objectstore-download@main
  with:
    api_key: ${{ secrets.DTZ_API_KEY }}
    name: build.zip
```

### Explicit `object_key`

If the object’s key differs from the filename you want on disk:

```yaml
- name: Pull release package
  uses: DownToZero-Cloud/objectstore-download@main
  with:
    api_key: ${{ secrets.DTZ_API_KEY }}
    name: latest-release.zip
    object_key: releases/2025-06-20/package.zip
```

## Outputs

This action does **not** expose any outputs. The downloaded file is saved in the runner’s working directory and is therefore available to all subsequent steps.

## Complete workflow

```yaml
name: CI → Download artefact & deploy

on:
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Download artefact from Objectstore
        uses: DownToZero-Cloud/objectstore-download@main
        with:
          api_key: ${{ secrets.DTZ_API_KEY }}
          name: build.zip

      - name: Unzip artefact
        run: unzip build.zip -d dist/

      - name: Deploy to production
        run: ./scripts/deploy.sh dist/
```

## License

[MIT](LICENSE)

Made with ❤️ by the DevOps team at **DownToZero.cloud**.
