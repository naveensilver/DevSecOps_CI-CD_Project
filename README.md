# DevSecOps_CI-CD_Project
### Real-Time DevSecOps Pipeline for a DotNet Web App

## <Architecture>


Hello friends, we will be deploying a .Net-based application. This is an everyday use case scenario used by several organizations. We will be using Jenkins as a CICD tool and deploying our application on a Docker Container and Kubernetes cluster. Hope this detailed blog is useful.

This project shows the detailed metric i.e CPU Performance of our instance where this project is launched.

## Steps:

Step 1 — Create an Ubuntu T2 Large Instance with 30GB storage

Step 2 — Install Jenkins, Docker and Trivy. Create a Sonarqube Container using Docker.

Step 3 — Install Plugins like JDK, Sonarqube Scanner

Step 4 — Install OWASP Dependency Check Plugins

Step 5 — Configure Sonar Server in Manage Jenkins

Step 6— Create a Pipeline Project in Jenkins using Declarative Pipeline

Step 7 — Install make package

Step 8— Docker Image Build and Push

Step 9 — Deploy the image using Docker

Step 10—Access the Real World Application

Step 11— Kubernetes Set Up

Step 12 — Terminate the AWS EC2 Instance

## References

### Now, lets get started and dig deeper into each of these steps :-

Step 1 — Launch an AWS T2 Large Instance. Use the image as Ubuntu. You can create a new key pair or use an existing one. Enable HTTP and HTTPS settings in the Security Group.

## <instance Image>

Step 2 — Install Jenkins, Docker and Trivy


- 2A — To Install Jenkins

Connect to your console, and enter these commands to Install Jenkins


```
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins

sudo apt update
sudo apt install openjdk-17-jdk
sudo apt install openjdk-17-jre

sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```

Once Jenkins is installed, you will need to go to your AWS EC2 Security Group and open Inbound Port 8080, since Jenkins works on Port 8080.

### <SG 8080 Image>

Now, grab your Public IP Address

```
<EC2 Public IP Address:8080>
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
Unlock Jenkins using an administrative password and install the required plugins.

### <Image >

Jenkins will now get installed and install all the libraries.

### < create admin Image >

Jenkins Getting Started Screen

### <Jenkins Dashboard image>

- 2B — Install Docker

```
sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER
sudo chmod 777 /var/run/docker.sock 
sudo docker ps
```
After the docker installation, we create a sonarqube container (Remember added 9000 port in the security group)

### <Image SG 9000>
```
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```
### <EC2 DOCKER COMMAND IMAGE>


### <LOGIN TO SONARQUBE>

USER NAME : admin

Pwd : admin

### SonarQube image


- 2C — Install Trivy

```
sudo apt-get install wget apt-transport-https gnupg lsb-release

wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null

echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list

sudo apt-get update

sudo apt-get install trivy -y
```

Step 3 — Install Plugins like JDK, Sonarqube Scanner, OWASP Dependency Check,

Goto Manage Jenkins →Plugins → Available Plugins →

Install below plugins

1 → Eclipse Temurin Installer (Install without restart)

2 → SonarQube Scanner (Install without restart)

Goto Manage Jenkins →Plugins → Available Plugins →

### <Jenkins Plugins image>

3B — Configure Java in Global Tool Configuration

Goto Manage Jenkins → Tools → Install JDK17→ Click on Apply and Save
### <Image>
Step 4 — GotoDashboard → Manage Jenkins → Plugins → OWASP Dependency-Check. Click on it and install without restart.

### image

First, we configured Plugin and next we have to configure Tool

Goto Dashboard → Manage Jenkins → Tools →

### image

Click on apply and Save here.

Grab the Public IP Address of your EC2 Instance, Sonarqube works on Port 9000, sp <Public IP>:9000. Goto your Sonarqube Server. Click on Administration → Security → Users → Click on Tokens and Update Token → Give it a name → and click on Generate Token


### image

Click on Update Token

### image

Create a token with a name and generate

Copy this Token

Goto Dashboard → Manage Jenkins → Credentials → Add Secret Text. It should look like this

### image

You will this page once you click on create

### image

Now, go to Dashboard → Manage Jenkins → Configure System

### image 


Click on Apply and Save

The Configure System option is used in Jenkins to configure different server

Global Tool Configuration is used to configure different tools that we install using Plugins

We will install a sonar scanner in the tools.

### image 

In the Sonarqube Dashboard add a quality gate also

Administration → Configuration →Webhooks

### image

Click on Create
### image
Add details

### image
Click on Apply and Save here.


Step 6 — Create a Pipeline Project in Jenkins using Declarative Pipeline


```
pipeline {
    agent any
    tools {
        jdk 'jdk17'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout From Git') {
            steps {
                git branch: 'main', url: 'https://github.com/writetoritika/DotNet-monitoring.git'
            }
        }
        stage("Sonarqube Analysis ") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh """$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Dotnet-Webapp \
                        -Dsonar.projectKey=Dotnet-Webapp"""
                }
            }
        }
        stage("quality gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                }
            }
        }
        stage("TRIVY File scan") {
            steps {
                sh "trivy fs . > trivy-fs_report.txt"
            }
        }
        stage("OWASP Dependency Check") {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --format XML ', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
    }
}
```


Click on Build now, you will see the stage view like this
### image
You will see that in status, a graph will also be generated and Vulnerabilities
### image

To see the report, you can go to Sonarqube Server and go to Projects

Step 7 — Install make package


```
sudo apt install make
# to check version install or not
make -v
```

Step 8 — Docker Image Build and Push

We need to install the Docker tool in our system, Goto Dashboard → Manage Plugins → Available plugins → Search for Docker and install these plugins

- Docker
- Docker Commons
- Docker Pipeline
- Docker API
- docker-build-step

and click on install without restart

Now, goto Dashboard → Manage Jenkins → Tools →

Add DockerHub Username and Password under Global Credentials

In the makefile, we already defined some conditions to build, tag and push images to dockerhub.


that’s why we are using make image and make a push in the place of docker build -t and docker push

Add this stage to Pipeline Script


```
stage("Docker Build & tag"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){   
                       sh "make image"
                    }
                }
            }
        }
        stage("TRIVY"){
            steps{
                sh "trivy image writetoritika/dotnet-monitoring:latest > trivy.txt" 
            }
        }
        stage("Docker Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){   
                       sh "make push"
                    }
                }
            }
        }
```

When all stages in docker are successfully created then you will see the result You log in to Dockerhub, and you will see a new image is created



### <image>

Stage view


Step 9 — Deploy the image using Docker

Add this stage to your pipeline syntax


You will see the Stage View like this,


(Add port 5000 to Security Group)

## image

And you can access your application on Port 5000. This is a Real World Application that has all Functional Tabs.

Step 10 — Access the Real World Application

### image 
Step 11 — Kubernetes Set Up

Take-Two Ubuntu 20.04 instances one for k8s master and the other one for worker.

Install Kubectl on Jenkins machine as well.
