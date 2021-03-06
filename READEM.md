# Using Declarative Jenkins Pipelines

These are my notes for the course

1. Using Declarative Jenkins Pipelines, by Elton Stoneman, pluralsight


The workflow plugin is the pipeline plugin.
Jenkins has environment variables that run as part of any job, for example <code>BUILD_NUMBER</code>. We can always access them in our pipeline scripts.

BlueOcean is an alternative Jenkins UI specifically designed for pipeline jobs.
The environment block, at the beginning or the end of the Jenkins file, defines environment variables for that Jenkins file that can be used on any step of any stage, eg:
```groovy
pipeline {
  agent any
  
  // environment variables at the pipeline level.
  // Any step will be able to read these values
  environment {
        NAME='Pepe'
        VERSION = '1'
  }
  
  stages {
    stage('stage1') {
      steps {
        
        // Variables inside a single quoted string don't get expanded eg
        echo 'This is the $BUILD_NUMBER of version $VERSION'
        // In BlueOcean, insert the echo command through a Shell Script step
        echo "This is the $BUILD_NUMBER of version $VERSION"
        
        sh '''
          echo "Using a multi-line shell step"
          chmod +x test.sh
          ./test.sh
        '''
        
      }
    }
  }
}
```
```bash
#!/bin/sh
echo "Inside the script, $NAME is running version $VERSION"
```
Environment variables in the <code>environment{...}</code> block are visible in scripts called from the pipeline.

Here we are in a simple pipeline project configured to take a Jenkins file from a git repo through a checkout. The repo will also contain the the bash script to be run. The checkout of the repo is done automatically.


## Pipeline structure
We'll typically have build, test and publish stages. Stages can define the agent that will run it, as well as environment variables scoped to it. Inside a stage, put the <code>steps{...}</code> block with "steps", such as <code>echo</code> and <code>sh</code>, which are the most common steps.

Jenkins uses groovy as the engine for pipeline jobs. In groovy single quoted strings are literals. Only double quoted strings can use variable interpolation to inject the value of variables. So for the <code>echo</code> step we have:
```groovy
echo 'This is a $VARIABLE'  // This is a $VARIABLE
echo "This is a $VARIABLE"  // This is a demo     MEMORIZE
echo "This is a ${VARIABLE}"// This is a demo     MEMORIZE 
```
Something similar happens with the shell step <code>sh</code>. In this example, we want to interpolate variables which are inside strings which are inside the multiline sh step.
```groovy
sh "echo This is a $VARIABLE"  // this works ok
sh 'echo This is a $VARIABLE'  // this also works ok
sh '''
  echo "This is a $VARIABLE"
  echo "This is a ${VARIABLE}"
'''
sh """
  echo 'This is a $VARIABLE'
  echo 'This is a ${VARIABLE}'    // this is environment variable
  echo 'This is a ${env.VARIABLE}'  // this is Groovy variable !!!
"""
```
All these will send "This is a demo" to the standard output, ie. the value of VARIABLE will be substituted.
Notice the difference between "environment variables" and Groovy variables.
But I need to understand what the <code>sh</code> step does.

Blocks agent and environment can be at the pipeline level or a a stage level. In at the stage level, will be limited to that stage.
```groovy
pipeline {
    agent any
    environment {
        RELEASE='20.04'
    }
    stages {
        stage('Build') {
            agent any
            environment {
                LOG_LEVEL='INFO'
            }
            steps {
                echo "Building release ${RELEASE} with log level ${LOG_LEVEL}..."
            }
        }
        stage('Test') {
            steps {
                echo "This is stage: $STAGE_NAME"
                echo "Testing. I can see release ${RELEASE}, but not log level ${LOG_LEVEL}"
            }
        }
    }
}
```
Notice that the environment variables are accessed without the <code>env.</code>. Notice also that inside a given stage, the name of the stage can be accessed through the environment variable <code>STAGE_NAME</code>.

## Interactive pipeline
We ask for user input in a pipeline with the <code>input</code> step. With it, we pause the build and wait for some sort of user confirmation.

```groovy
pipeline {
    agent any
    environment {
        RELEASE='20.04'
    }
    stages {
        stage('Build') {
            agent any
            environment {
                LOG_LEVEL='INFO'
            }
            steps {
                echo "Building release ${RELEASE} with log level ${LOG_LEVEL}..."
            }
        }
        stage('Test') {
            steps {
                echo "Testing release ${RELEASE}..."
            }
        }
        stage('Deploy') {
            input {
                message 'Do you want to deploy??'
                ok 'Do it!'
                parameters {
                    string(name: 'TARGET_ENVIRONMENT', defaultValue: 'PROD', description: 'Target deployment environment')
                }
            }
            steps {
                echo "Deploying release ${RELEASE} to environment ${TARGET_ENVIRONMENT}"
            }
        }
        stage ('Hello') {
            steps {
              echo "This was the release $RELEASE"
              // echo "The target environment is: " + "$TARGET_ENVIRONMENT" // Error. Variable TARGET_ENVIRONMENT will not be visible here
            }
        }       
    }
    post {
        always {
             echo 'Prints whether deploy happened or not, success or failure'
        }
    }
}
```
Notice how what the user will write in the dialog will be captured in the variable <code>TARGET_ENVIRONMENT</code>, which we then use in the steps block of the same stage. This variable will not be visible in other stages. Notice also that in this case we don't assign what the input block returns to any variable.

If we click in Abort, the steps block of the stage with the input block will not be executed, and the whole pipeline will be "Aborted". The rest of the stages it may have will be skipped. However, the post step will still be run, so we can put on it any notification or clean up job. In the example, we run the post block <code>always</code>, ie. <u>whatever</u> the outcome of the pipeline is (FAILURE, SUCCESS etc). In the post bloc we could have a clean up step/stage? which always run, and a notification step/stage ? that only runs if the build fails.

![image info](./pictures/input_step.png)

A pipeline is only the structure for modeling our build workflow. For the actual mechanics of the build, we'll still need to use our common build tools, such as Maven, either through shell steps or through plugins. 

Other than echo and sh, other common steps are retry and timeout. Everything else come from plugins. Plugins that are pipeline compatible are steps that we can use in our stages, such as junit, unzip, kubernatesDeploy etc. <b>Plugins</b> in Jenkins will give a myriad of possibilities, in the same fashion of Ansible plugins. See course Using and Managing Jenkins Plugins, in pluralsight.




## Running stages in parallel
We can nest stages inside another stage and run them in parallel, potentially on different agent, which is a big performance boost. These nested stages will need to be inside a <code>parallel{...}</code> block. In the example bellow all the parallel stages run on the same (unique) agent, in parallel. 
```groovy
pipeline {
    agent any
    environment {
        RELEASE='20.04'
    }
    stages {
        stage('Build') {
            environment {
                LOG_LEVEL='INFO'
            }
            parallel {
                stage('linux-arm64') {
                    steps {
                        echo "Building release ${RELEASE} for ${STAGE_NAME} with log level ${LOG_LEVEL}..."
                    }
                }
                stage('linux-amd64') {
                    steps {
                        echo "Building release ${RELEASE} for ${STAGE_NAME} with log level ${LOG_LEVEL}..."
                    }
                }
                stage('windows-amd64') {
                    steps {
                        echo "Building release ${RELEASE} for ${STAGE_NAME} with log level ${LOG_LEVEL}..."
                    }
                }
            }
        }
        stage('Test') {
            steps {
                echo "Testing release ${RELEASE}..."
            }
        }
        stage('Deploy') {
            input {
                message 'Deploy?'
                ok 'Do it!'
                parameters {
                    string(name: 'TARGET_ENVIRONMENT', defaultValue: 'PROD', description: 'Target deployment environment')
                }
            }
            steps {
                echo "Deploying release ${RELEASE} to environment ${TARGET_ENVIRONMENT}"
            }
        }        
    }
    post{
        always {
             echo 'Prints whether deploy happened or not, success or failure'
        }
    }
}
```

The following Jenkinsfile illustrate other core steps in a Jenkins file.

```groovy
pipeline {
    agent any
    environment {
      RELEASE='20.04'
    }
   stages {
      stage('Build') {
            environment {
               LOG_LEVEL='INFO'
            }
            steps {
               echo "Building release ${RELEASE} with log level ${LOG_LEVEL}..."
            }
        }
      stage('Test') {
            environment {
               FILE_CONTENT = "This is the content I want to write in the file.\n" +
                                "It is a very important content as you can see."
            }
            steps {
               echo "Testing release ${RELEASE}"
               writeFile file: 'test-results.txt', text: "${FILE_CONTENT}"               
            }
        }
   }
   post {
      success {
         archiveArtifacts 'test-results.txt'
      }
   }
}
```
The Jenkins home is usually ~ = /var/jenkins_home. For a given project Jenkins stores data in the file system under the <code>~/jobs</code> and <code>~/workspace</code> directories.
```bash
~/workspace$ ls -larth ~/workspace/
total 76K
drwxr-xr-x  7 jenkins jenkins 4.0K Apr 28 18:56 test
drwxr-xr-x  8 jenkins jenkins 4.0K May 14 20:44 UsingDeclarativeJenkinsPipelines
```
```bash
$ ls -larth ~/jobs
total 40K
drwxr-xr-x  3 jenkins jenkins 4.0K Apr 29 11:28 test
drwxr-xr-x  3 jenkins jenkins 4.0K May 12 07:15 UsingDeclarativeJenkinsPipelines
```
The workspace will have what was checked out from the remote git repository as well as any other file generated in the current build. For example:
```bash
~/workspace/UsingDeclarativeJenkinsPipelines/demo3-1$ ls -larth
total 40K
drwxr-xr-x 8 jenkins jenkins 4.0K May 14 20:44 ..
drwxr-xr-x 5 jenkins jenkins 4.0K May 14 20:44 course_materials
drwxr-xr-x 2 jenkins jenkins 4.0K May 14 20:44 pictures
drwxr-xr-x 4 jenkins jenkins 4.0K May 14 20:44 m2
-rw-r--r-- 1 jenkins jenkins 9.8K May 14 20:58 READEM.md
drwxr-xr-x 8 jenkins jenkins 4.0K May 14 20:58 .git
drwxr-xr-x 6 jenkins jenkins 4.0K May 14 20:58 .
-rw-r--r-- 1 jenkins jenkins   95 May 14 20:58 test-results.txt
```
This is a pipeline project called "demo3-1" placed inside a Jenkins GUI folder "UsingDeclarativeJenkinsPipelines".

Inside the jobs directory instead we have the configuration file <code>config.xml</code> for the project, as well as permanently, I think, files stored for each build of our project, under the <code>build</code> directory. For example: 
```bash
~/jobs/UsingDeclarativeJenkinsPipelines/jobs/demo3-1$ ls -larth
total 20K
drwxr-xr-x  9 jenkins jenkins 4.0K May 14 20:30 ..
-rw-r--r--  1 jenkins jenkins 1.6K May 14 20:30 config.xml
-rw-r--r--  1 jenkins jenkins    3 May 14 20:58 nextBuildNumber
drwxr-xr-x  3 jenkins jenkins 4.0K May 14 20:58 .
drwxr-xr-x 11 jenkins jenkins 4.0K May 14 20:58 builds
```
```bash
~/jobs/UsingDeclarativeJenkinsPipelines/jobs/demo3-1$ ls -larth builds/
total 48K
drwxr-xr-x  4 jenkins jenkins 4.0K May 14 20:44 1
drwxr-xr-x  4 jenkins jenkins 4.0K May 14 20:47 2
drwxr-xr-x  2 jenkins jenkins 4.0K May 14 20:47 3
drwxr-xr-x  2 jenkins jenkins 4.0K May 14 20:48 4
drwxr-xr-x  3 jenkins jenkins 4.0K May 14 20:50 5
drwxr-xr-x  3 jenkins jenkins 4.0K May 14 20:51 6
drwxr-xr-x  4 jenkins jenkins 4.0K May 14 20:52 7
drwxr-xr-x  2 jenkins jenkins 4.0K May 14 20:57 8
drwxr-xr-x  4 jenkins jenkins 4.0K May 14 20:58 9
```
```bash
~/jobs/UsingDeclarativeJenkinsPipelines/jobs/demo3-1$ ls -larth builds/9/
total 56K
-rw-r--r--  1 jenkins jenkins  536 May 14 20:58 changelog5295595542662310757.xml
drwxr-xr-x  2 jenkins jenkins 4.0K May 14 20:58 archive
-rw-r--r--  1 jenkins jenkins   65 May 14 20:58 log-index
drwxr-xr-x  2 jenkins jenkins 4.0K May 14 20:58 workflow
-rw-r--r--  1 jenkins jenkins  15K May 14 20:58 log
drwxr-xr-x 11 jenkins jenkins 4.0K May 14 20:58 ..
-rw-r--r--  1 jenkins jenkins  15K May 14 20:58 build.xml
drwxr-xr-x  4 jenkins jenkins 4.0K May 14 20:58 .
```
```bash
~/jobs/UsingDeclarativeJenkinsPipelines/jobs/demo3-1$ ls -larth builds/9/archive
total 12K
-rw-r--r-- 1 jenkins jenkins   95 May 14 20:58 test-results.txt
```

If our pipeline "archives" something, as the last Jenkinsfile shown does, eg. a file <code>test-results.txt</code>, that file will be found in the archive directory of each build that created it, and also in the workspace directory, if the current build created it as well:
```bash
$ cd ~
$ find . -name test-results.txt
./jobs/UsingDeclarativeJenkinsPipelines/jobs/demo3-1/builds/7/archive/test-results.txt
./jobs/UsingDeclarativeJenkinsPipelines/jobs/demo3-1/builds/9/archive/test-results.txt
./jobs/UsingDeclarativeJenkinsPipelines/jobs/demo3-1/builds/1/archive/test-results.txt
./jobs/UsingDeclarativeJenkinsPipelines/jobs/demo3-1/builds/2/archive/test-results.txt
./workspace/UsingDeclarativeJenkinsPipelines/demo3-1/test-results.txt
```
The <code>archiveArtifact</code> step takes a file from the workspace and archives it in the file system of the Jenkins node, as an artifact for that run of the job.

The writeFile and archiveArtifact steps are part of the Pipeline plugin.

Plugins usually make available more steps.

## Credentials in Jenkins


We use credential to log to remote application or services and use them. For example AWS cloud or Github (eg. a github token). In Jenkins credentials are globally managed, ie. they can be used through Jenkins everywhere of inside pipeline projects. Normally the administrator user of the control node (I think) will have access to all credential. Other users will be granted different type of access to the credentials by the administrator. If nothing is configured, all user will get all type of access to credentials. The administrator configure these accesses under Dashboard/Configure Global Security/Authorization.

Plugins are types of credentials to Jenkins. Common type of credentials are:
1. Username with password: stored in the form of username:password, for any application.
2. SSH Username with private key, for example to have access to a SCM system.
3. Secret file ? or secret text stored in a file
4. Secret text, eg. a Github api token, of cloud api token. These tokens are stored in the form of secret texts.
5. Certificate ?

Credentials are all stored encrypted in the master node and are referenced by its Id, eg
```groovy
environment {
	AWS_ACCESS_KEY_ID = credentials ('jenkins-aws-secret-key-id')
	AWS_SECRET_ACCESS_KEY = credentials ('jenkins-aws-secret-access-key')
}
```

Learn more about jenkins credentials


use "def" for scripted pipelines. 

(2) start 8.45
