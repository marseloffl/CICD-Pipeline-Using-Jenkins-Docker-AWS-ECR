pipeline {
    agent any
    environment {
        registry = "364028079483.dkr.ecr.ap-south-1.amazonaws.com/samplewebapp"
    }

    stages{
        stage("Clone Git Repository"){
            steps{
                //checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/marseloffl/CICD-Pipeline-Using-Jenkins-Docker-AWS-ECR.git']])
                git url: "https://github.com/marseloffl/CICD-Pipeline-Using-Jenkins-Docker-AWS-ECR.git", branch: "master"
            }
        }
    
    // Building Docker Image
    stage('Build') {
      steps{
        script {
          //dockerImage = docker.build registry
            sh 'docker build -t samplewebapp .'
        }
      }
    }

    // Login, Tag your Built Image and Push it to AWS ECR
    stage('Push') {
     steps{  
         script {
                sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 364028079483.dkr.ecr.ap-south-1.amazonaws.com'
                sh 'docker tag samplewebapp:latest 364028079483.dkr.ecr.ap-south-1.amazonaws.com/samplewebapp:latest'
                sh 'docker push 364028079483.dkr.ecr.ap-south-1.amazonaws.com/samplewebapp:latest'
         }
        }
      }

    // Stopping Previous Container if anything else running
    //stage('Stop') {
    //  steps {
    //        sh 'docker ps -f name=to-do-list-app -q | xargs --no-run-if-empty docker container stop'
    //        sh 'docker container ls -a -fname=to-do-list-app -q | xargs -r docker container rm'
    //     }
    //   }

    stage('Docker Run') {
     steps{
         script {
                sh 'docker run -d -p 8000:8000 --rm --name to-do-list-app 364028079483.dkr.ecr.ap-south-1.amazonaws.com/samplewebapp'
            }
      }
    }
    }
}
