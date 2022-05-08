# Demo 3

Adding pipeline build functionality


## Pre-reqs

Run Jenkins in [Docker](https://www.docker.com/products/docker-desktop):

```
docker-compose -f jenkins/docker-compose.yml up -d
```

> Configure the [Slack plugin](https://slack.com/apps/A0F7VRFKN-jenkins-ci)

> Add credential `an-api-key`, secret text with any value

## 3.1 Writing and archiving output

Log into Jenkins at http://localhost:8080 with `psod`/`psod`.

- Copy item, `demo3-1` from `demo2-1`
- Path to Jenkinsfile `m2/demo3/3.1/Jenkinsfile`

> Walk through the [Jenkinsfile](./3.1/Jenkinsfile)

- Run and check 
- Verify output file in job results

## 3.2 Using plugins - credentials and Slack

Back to http://localhost:8080

- Copy item, `demo3-2` from `demo3-1`
- Path to Jenkinsfile `m2/demo3/3.2/Jenkinsfile`

> Walk through the [Jenkinsfile](./3.2/Jenkinsfile)

- Run and check
- Credential masked in shell output
- Notification sent to [Slack](https://app.slack.com/client/TV8G80XJL/C011X6EGT3N)

## 3.3 Script stages

Back to http://localhost:8080

- Copy item, `demo3-3` from `demo3-1`
- Path to Jenkinsfile `m2/demo3/3.3/Jenkinsfile`

> Walk through the [Jenkinsfile](./3.3/Jenkinsfile)

- Run and check
- Script block needs approval (twice)
- Script fails 50% of time
- Repeat runs - [Slack](https://app.slack.com/client/TV8G80XJL/C011X6EGT3N) notification different for success and failure