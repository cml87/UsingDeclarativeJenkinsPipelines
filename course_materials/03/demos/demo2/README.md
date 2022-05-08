# Demo 2

Writing and using shared libraries.

## Pre-reqs

Run Jenkins in [Docker](https://www.docker.com/products/docker-desktop):

```
docker-compose -f ../jenkins/docker-compose.yml up -d
```

## 2.1 Using a shared library

- Reminder - `auditTools` function from [1.3 Jenkinsfile](../demo1/1.3/Jenkinsfile)
- Moved shared library in [auditTools.groovy](../shared-library/vars/auditTools.groovy)
- Published to https://github.com/sixeyed/jenkins-pipeline-demo-library

> Used in [2.1 Jenkinsfile](./2.1/Jenkinsfile)

- Copy item, `demo2-1` from `demo1-1`
- Path to Jenkinsfile `m3/demo2/2.1/Jenkinsfile`
- Open in Blue Ocean
- Run
- Check pipeline log - fetches library

## 2.2 Failures in shared pipelines

- Copy item, `demo2-2` from `demo2-1`
- Path to Jenkinsfile `m3/demo2/2.1/Jenkinsfile`
- Open in Blue Ocean
- Run - fails
- Check pipeline log

> Walk through the [2.2 Jenkinsfile](./2.2/Jenkinsfile)

- Check library method [auditTools2.groovy](../shared-library/vars/auditTools2.groovy)
- Fix quotes & push GitHub repo
- Build again - passes, no change to code or pipeline

## 2.3 Shared libraries in a full build

- Compare 1.3 Jenkinsfile and [2.3 Jenkinsfile](./2.3/Jenkinsfile)
- Copy item, `demo2-3` from `demo2-1`
- Path to Jenkinsfile `m3/demo2/2.3/Jenkinsfile`
- Build now
- Run

> Alternative - folder and global libraries

- New item, folder, `m3`
- Expand _Pipeline libraries_; implicit load but untrusted

- _Manage Jenkins_ ... _Configure System_
- Expand _Global Pipeline libraries_; implicit load and trusted