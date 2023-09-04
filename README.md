# CICD Pipeline Using Git, Jenkins, Docker Container and AWS Cloud to deploy real world application

## Purpose:
* To Automate the Whole CICD Pipeline, Push image to AWS Elastic Container Registry and Deploy web application in Docker using Jenkinsfile. 

## Prerequisite:
* AWS Cloud and GitHub Account
* AWS IAM User [Attach EC2InstanceFullAccess and ECR Container Builds Policy]
* AWS IAM Role
![IAM-Role](https://drive.google.com/uc?export=view&id=1DgVBz7MRANGIvXn_mLctdTWBm7L2mI7D)
* Repository Created in AWS ECR to push/store the built image.
![CreatedECR](https://drive.google.com/uc?export=view&id=1jhs0t1K9yn5F1p9nLwJa9QtDmxxIkBci)

## Step 1. Push Source Code to remote repository(GitHub)

* You can clone my source code to your local desktop.
* Using git clone command.
```git
git clone https://github.com/marseloffl/CICD-Pipeline-Using-Jenkins-Docker-AWS-ECR.git
```
* Alter Jenkinsfile and provide details correctly based on your ECR. Don't use same as mine, it won't work for you and i will delete it after my demonstration.
* After your Code is ready Push it to your remote repository.

## Step 2. Launch an EC2 Instance

* I chose Ubuntu image for this project, you can choose any linux distro or Amazon linux.
* Attach IAM Role before or after launching. Only then EC2 Instance can communicate with another service called Elastic Container Registry.
* On Security group enable only Port 22 for ssh connection. Later we will edit the inbound rule and enable two more ports.
* SSH and Login to your EC2 machine.
* Install [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) on your EC2 Machine and run `aws configure`, then provide your IAM User Access ID, Sceret Code to Login, Zone and format to login.

## Step 3. Install Jenkins
* Install Java before installing jenkins. Follow this Doc and install Properly. 
[Debian/Ubuntu](https://www.jenkins.io/doc/book/installing/linux/#debianubuntu)
[Redhat/CentOS](https://www.jenkins.io/doc/book/installing/linux/#red-hat-centos)
* Jenkins will run on port 8080, so enable this port on inbound rule security group on AWS.
* After installing jenkins, login with initial password and Create new Login Username and Password.
* Install AWS Credential Plugin on Jenkins Plugin section, Go to Credential  section add new credential after installing the plugin.
![IAMUSER](https://drive.google.com/uc?export=view&id=1nS02oyzA3h1FvKq9rcx2DyvSfJG7KFQy)
* Then Create New Job with any name, i have given samplewebapp and select pipeline project then click ok.
![JenkinsJob](https://drive.google.com/uc?export=view&id=1T_n2rgUCsE1trFPUhDvtF82Y0u3y5Zo2)
* Select pipeline script from SCM from Pipeline Section. Add Git Repository and add Add git credentials to link Git and Jenkins. 
![PipelineSetup](https://drive.google.com/uc?export=view&id=1A2rAo-ioCiR5wN1VdGpA27RCP-9Y-51F)
* Then scriptpath must have Jenkinsfile and select apply & save.
* Click Build Now, Yes you will see and error because we didn't run the code for our app and didn't install Docker.
* Check the console output, you will get the path to jenkins workspace. Go to EC2 Machine and run the below command.
```
cd /var/lib/jenkins/workspace/samplewebapp
```
After entered to the workspace directory, run below command one by one.
```
sudo apt install nodejs
```
```
sudo apt install npm
```
```
sudo npm install
```
```
node app.js
```
* Now you can check the app is Working on Port http://public-ip-address:8000. If it doesn't run, Enable Port 8000 on your security group inbound rule on AWS. Click Ctrl+C, then it won't work. Dont Panic!!!

## Step 4. Install Docker
To Install Docker run the command
```bash
sudo apt get update
sudo apt install docker.io
```
After running the above commands you have enable permission for some files to execute built, push and deploy.
```
sudo chmod 777 /var/lib/jenkins/workspace/samplewebapp
```
```
sudo usermod -a -G docker $USER
```
```
sudo chmod 666 /var/run/docker.sock
```

## Step 5. Go to Jenkins and Build Now.
* Now our script in Jenkinsfile will clone our source code, build it, Login to AWS ECR with credentials, Push the built image to ECR and finally it will Deploy our app and it will run on container successfully.
* Check the Console Output for Success Message
![Built&Tagged](https://drive.google.com/uc?export=view&id=1YXg3atlhHXgMVj7gfadZPc4u7M5jQWVe)
![Success](https://drive.google.com/uc?export=view&id=1_phg7DlbgDMV-RANrS6kDGaQ6PeQ7Xru)
* App in running on Container, we check it with port assigned in Jenkinsfile
http://public-ip-address:8000
![Deployed](https://drive.google.com/uc?export=view&id=1ePY0xw8hRpbd6Ba2iTan-uQp9ExQVoUe)
* Also the Built Image is Pushed to ECR. 
![ECR](https://drive.google.com/uc?export=view&id=1FsOzVi47-RvNSPRp656UL9AoOyB1jgip)

## FAQ

### 1. Is it necessary to create an IAM Role ?

Yes, only then the communication between two service will be happened.

### 2. Is it necessary to create an IAM User ?

Yes, Install AWS Cli on EC2 Server and login with credentials also the intergration must be done with Jenkins Using AWS Credential Plugin. Error may occur without IAM User during image push to ECR.
 
### 3. Can i use your node app source code to deploy ?

Yes, you can also change it if needed.

### 4. Can i use your Jenkinsfile ?

No, it won't work. I'll Delete it & also you need credentials to access.
Check the Jenkinsfile Script Template file, alter it and use it according to your configuration. Don't simply copy paste.

### 5. Can i use your Dockerfile ?

Yes, but if you are deploying some other language based app, then you need to change it.

### 6. When Error occurs most of the time ?

If the file execution permission in not changed then the error may occur on Jenkins Console Output. I have mentioned above where to change the permission.
If the integration through login credentials was not proper then error may occur. 