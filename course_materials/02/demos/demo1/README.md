# Demo 1

Creating and running simple pipelines.

## Pre-reqs

Run Jenkins in [Docker](https://www.docker.com/products/docker-desktop):

```
docker-compose -f jenkins/docker-compose.yml up -d
```

> Plugins configured in [install-plugins.groovy](../jenkins/20.04/scripts/install-plugins.groovy)

## 1.1 Create a simple pipeline

Log into Jenkins at http://localhost:8080 with `psod`/`psod`.

- New item, pipeline, `demo1-1`
- Select sample pipeline script
- Replace echo with `echo "This is build number $BUILD_NUMBER"`
- Run and check output

## 1.2 Create a pipeline with Blue Ocean

Browse to http://localhost:8080/blue

- New pipeline
- Bitbucket repo - https://sixeyed@bitbucket.org/sixeyed/demo1-2.git
- Log in with Bitbucket creds

> Needs write access to repo

- Build pipeline
- Add environment variable `DEMO=1`
- Add stage
- Add _Print message_ `This is build $BUILD_NUMBER of demo $DEMO`
- Run and check: doesn't interpolate strings
- View Jenkinsfile in repo:editor only uses single quotes
- Replace with shell script `echo "This is build $BUILD_NUMBER of demo $DEMO"`

## 1.3 Create a pipeline from Git

Back in the classic UI http://localhost:8080

- New item, pipeline, `demo1-3`
- Select pipeline from source control
- Git - https://github.com/sixeyed/jenkins-pipeline-demos.git

> Walk through the [Jenkinsfile](./1.3/Jenkinsfile)

- Run and check 
- Open in blue ocean
- Repeat stage