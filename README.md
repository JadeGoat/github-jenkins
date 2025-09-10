# Configure Github with Jenkins
#### Setup Jenkins
1. Under Pipeline
   - Definition: Pipeline script from SCM
   - SCM: Git
   - Repository URL: git_url
     - No need credential if public, setup credential if private
   - Branches to build: */main
2. Under Trigger
   - Pull SCM: Checked
   - Schedule: Cron Expression
     - Format: minute  hour   day_of_the_month  month   day_of_the_week
     - Format:  (0-59) (0-23)      (1-31)        (1-12)      (0-6)
     - example
       - H * * * *    => Poll once every hour
       - H/2 * * * *  => Poll every 2 minutes
3. Others
   Under Genernal
   - Discard old build: Checked
   - Max # of builds to keep: 2
   

#### Setup Github
Ensure that the Git repository have Jenkinsfile (case sensitity)
Sample example of Jenkinsfile
```
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
```
