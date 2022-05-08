# Demo 3

Using multi-branch pipelines.

## Pre-reqs

Run Jenkins in [Docker](https://www.docker.com/products/docker-desktop):

```
docker-compose -f ../jenkins/docker-compose.yml up -d
```

## 3.1 Multi-branch pipelines

Log into Jenkins at http://localhost:8080 with `psod`/`psod`.

- New item _Multibranch Pipeline_ `demo3`
- Branch source Git `https://github.com/sixeyed/jenkins-pipeline-demos.git`
- Untick _Discard old items_

> _Scan Multibranch Pipeline Now_

## 3.2 Adding a branch

Create a branch locally and push:

```
git checkout -b m4
git push origin m4
```

> _Scan Multibranch Pipeline Now_

Edit Jenkinsfile and push changes

> Builds diverge

## 3.3 Removing a branch

Remove the new branch locally and push:

```
git checkout master
git push -d origin m4
```
> _Scan Multibranch Pipeline Now_