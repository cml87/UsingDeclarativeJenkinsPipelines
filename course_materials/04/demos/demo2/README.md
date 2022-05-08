# Demo 2

Shared pipelines and the Job DSL plugin.

## Pre-reqs

Run Jenkins in [Docker](https://www.docker.com/products/docker-desktop):

```
docker-compose -f ../jenkins/docker-compose.yml up -d
```

## 2.1 Pipelines in shared libraries

> Walk through the [simple shared pipeline](../shared-library/vars/simplePipeline.groovy) and  [Jenkinsfile](./2.1/Jenkinsfile).

Log into Jenkins at http://localhost:8080 with `psod`/`psod`.

- Copy item, `demo2-1` from `demo1-1`
- Path to Jenkinsfile `m4/demo2/2.1/Jenkinsfile`
- Run
- Check output

## 2.2 A shared build pipeline

- Credentials in Jenkins - Docker client cert

> Walk through the [cross-platform shared pipeline](../shared-library/vars/crossPlatformBuild.groovy) and  [Jenkinsfile](./2.2/Jenkinsfile).

- Copy item, `demo2-2` from `demo2-1`
- Path to Jenkinsfile `m4/demo2/2.2/Jenkinsfile`
- Run
- Check output

## 2.3 Generating multiple pipeline jobs

> Walk through the [job DSL Groovy script](./2.3/job_dsl.groovy)

- New item, folder `m4`
- New freestyle job `__seed`
- Add SCM ``
- Add build step _Process Job DSLs_
- DSL scripts `m4/demo2/2.3/job_dsl.groovy`

- Build
- Allow script
- Build again

- Check generated jobs
- Run them