# Demo 1

Clean code - refactoring pipelines.

## Pre-reqs

Run Jenkins in [Docker](https://www.docker.com/products/docker-desktop):

```
docker-compose -f ../jenkins/docker-compose.yml up -d
```

## 1.1 A real build pipeline

Log into Jenkins at http://localhost:8080 with `psod`/`psod`.

- New item, pipeline, `demo1-1`
- Select pipeline from source control
- Git - https://github.com/sixeyed/jenkins-pipeline-demos.git
- Path to Jenkinsfile  - `m3/demo1/1.1/Jenkinsfile`
- Open in Blue Ocean
- Run

> Walk through the [Jenkinsfile](./1.1/Jenkinsfile)

## 1.2 Adding parameters for RC build

- Copy item, `demo1-2` from `demo1-1`
- Path to Jenkinsfile `m3/demo1/1.2/Jenkinsfile`
- Open in Blue Ocean
- Run (first build doesn't show option)

> Walk through the [Jenkinsfile](./1.2/Jenkinsfile)

- Run again - _RC = no_
- Run again - _RC = yes_

> Check logs and artifacts

## 1.3 Moving pipeline logic into Groovy methods

- Copy item, `demo1-3` from `demo1-1`
- Path to Jenkinsfile `m3/demo1/1.3/Jenkinsfile`
- Build now
- Run

> Walk through the [Jenkinsfile](./1.3/Jenkinsfile)

- Open in Blue Ocean
- Run again - _RC = no_
- Run again - _RC = yes_

> Check logs and artifacts