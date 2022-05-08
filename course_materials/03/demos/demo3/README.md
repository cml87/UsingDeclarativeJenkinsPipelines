# Demo 3

Development workflow for pipelines and shared libraries.

## Pre-reqs

Run Jenkins in [Docker](https://www.docker.com/products/docker-desktop):

```
docker-compose -f ../jenkins/docker-compose.yml up -d
```

## 3.1 Jenkinsfile Linter

Jenkins API for validating pipeline syntax

```
curl -X POST -F "jenkinsfile=<./Jenkinsfile" http://localhost:8080/pipeline-model-converter/validate
```
> Usually need a crumb for CRSF protection

- Catches syntax errors
- Try quote mismatch
- Missing brackets for call
- Doesn't catch missing methods

> VS Code linter integration

- _Extensions_ - search `Jenkinsfile`
- Select _Jenkins Pipeline Linter Connector_
- _F1_ in Jenkinsfile

## 3.2 Pipeline Replay & Restart

- Open `demo2-2` build 1
- _Restart from stage_ uses original lib
- _Replay_ allows edit
- Edited replay scripts are preserved in restart

## 3.3 Pipeline Unit Test framework

> https://github.com/jenkinsci/JenkinsPipelineUnit

- Supports running pipelines and library methods
- Can mock steps and validate calls

- Import library in [Gradle build](../shared-library/build.gradle)
- Run test in [AuditToolsTest.groovy](../shared-library/test/vars/AuditToolsTest.groovy)
- Regression compared to expected output in [AuditToolsTest_default.txt](../shared-library/test/vars/callstacks/AuditToolsTest_default.txt)

```
docker build -t shared-library ../shared-library
```

> Build just for testing, compiled output not used in pipelines