## Jenkinsfile Script Template

* Alter it according to your configuration and use it.
* Changes to be done on ACC-ID, REGION, YOUR-GIT-REMOTE-REPOSITORY-LINK, YOUR-ECR-REPO & TO-NAME-THE-CONTAINER

```
pipeline {
    agent any
    environment {
        registry = "ACCT-ID.dkr.ecr.REGION.amazonaws.com/YOUR-ECR-REPO"
    }

    stages{
        stage("Clone Git Repository"){
            steps{
                git url: "YOUR-GIT-REMOTE-REPOSITORY-LINK", branch: "master"
            }
        }
    
    // Building Docker Image
    stage('Build') {
      steps{
        script {
          //dockerImage = docker.build registry
            sh 'docker build -t YOUR-REPO-NAME .'
        }
      }
    }

    // Login, Tag your Built Image and Push it to AWS ECR
    stage('Push') {
     steps{  
         script {
                sh 'aws ecr get-login-password --region REGION | docker login --username AWS --password-stdin ACCT-ID.dkr.ecr.REGION.amazonaws.com'
                sh 'docker tag YOUR-REPO-NAME:latest ACCT-ID.dkr.ecr.REGION.amazonaws.com/YOUR-ECR-REPO:latest'
                sh 'docker push ACCT-ID.dkr.ecr.REGION.amazonaws.com/YOUR-ECR-REPO:latest'
         }
        }
      }

    stage('Docker Run') {
     steps{
         script {
                sh 'docker run -d -p 8000:8000 --rm --name TO-NAME-THE-CONTAINER ACCT-ID.dkr.ecr.REGION.amazonaws.com/YOUR-ECR-REPO'
            }
      }
    }
    }
}
```