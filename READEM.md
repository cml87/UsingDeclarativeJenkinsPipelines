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
  
  environment {
        NAME='Pepe'
        VERSION = '1'
  }
  
  stages {
    stage('stage1') {
      steps {
        
        // Variables inside a single quoted string don't get expanded eg
        echo 'This is the $BUILD_NUMBER of demo $VERSION'
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
Environment variables in the <code>environment{...}</code> block are visible in scripts called from the pipeline. Here we are using running a Jenkins file in a git repo, having also the bash script to be run
