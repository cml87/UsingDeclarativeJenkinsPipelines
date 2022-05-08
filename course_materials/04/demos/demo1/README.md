# Demo 1

Powering pipelines with Docker.

## Pre-reqs

Run Jenkins in [Docker](https://www.docker.com/products/docker-desktop):

```
docker-compose -f ../jenkins/docker-compose.yml up -d
```

## 1.1 Using a container as a build agent

Log into Jenkins at http://localhost:8080 with `psod`/`psod`.

- New item, pipeline, `demo1-1`
- Select pipeline from source control
- Git - https://github.com/sixeyed/jenkins-pipeline-demos.git
- Path to Jenkinsfile  - `m4/demo1/1.1/Jenkinsfile`
- Run

> Walk through the [Jenkinsfile](./1.1/Jenkinsfile)

## 1.2 Custom container agents

- Copy item, `demo1-2` from `demo1-1`
- Path to Jenkinsfile `m4/demo1/1.2/Jenkinsfile`
- Run - fails

> Walk through the [Dockerfile](../Dockerfile) and the [Jenkinsfile](./1.2/Jenkinsfile)

> Walk through the fixed [Dockerfile.sdk](../Dockerfile.sdk) and [Jenkinsfile.fixed](./1.2/Jenkinsfile.fixed)

- Change Jenkinsfile to `m4/demo1/1.2/Jenkinsfile.fixed`
- Run again

## 1.3 The Docker pipeline plugin

- Credentials in Jenkins - Docker Hub

- Copy item, `demo1-3` from `demo1-1`
- Path to Jenkinsfile `m4/demo1/1.3/Jenkinsfile`
- Run

> Walk through the [Jenkinsfile](./1.3/Jenkinsfile) and [Dockerfile](../Dockerfile)
