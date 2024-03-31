# DevSecOps_CI-CD_Project

### Real-Time DevSecOps Pipeline for a DotNet Web App

## Architecture

![DevSecOps_Dot-Net_Web-App](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/6efe4fe9-17ee-41b2-87fd-190b1d64699a)

Hello friends, we will be deploying a .Net-based application. This is an everyday use case scenario used by several organizations. We will be using Jenkins as a CICD tool and deploying our application on a Docker Container and Kubernetes cluster. Hope this detailed blog is useful.

This project shows the detailed metric i.e CPU Performance of our instance where this project is launched.

## Steps:

Step 1 — Create an Ubuntu T2 Large Instance with 30GB storage

Step 2 — Install Jenkins, Docker and Trivy. Create a Sonarqube Container using Docker.

Step 3 — Install Plugins like JDK, Sonarqube Scanner

Step 4 — Install OWASP Dependency Check Plugins

Step 5 — Configure Sonar Server in Manage Jenkins

Step 6 — Create a Pipeline Project in Jenkins using Declarative Pipeline

Step 7 — Install make package

Step 8 — Docker Image Build and Push

Step 9 — Deploy the image using Docker

Step 10 — Access the Real World Application

Step 11 — Kubernetes Set Up

Step 12 — Terminate the AWS EC2 Instance

## References

### Now, lets get started and dig deeper into each of these steps :-

Step 1 — Launch an AWS T2 Large Instance. Use the image as Ubuntu. You can create a new key pair or use an existing one. Enable HTTP and HTTPS settings in the Security Group.

![Screenshot 2024-03-29 133456](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/f09c48fc-4fe8-4c7c-a51c-b73f0d7e7596)

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

![Screenshot 2024-03-29 135032](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/7ba68032-ac99-455d-91de-b7b93914202d)

Now, grab your Public IP Address

```
<EC2 Public IP Address:8080>
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
Unlock Jenkins using an administrative password and install the required plugins.

![4](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/dc7b74b6-c99e-491d-935e-411d6bfca1e9)

Jenkins will now get installed and install all the libraries and follow below steps:

![5](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/41a17547-9fea-4421-8346-1ff30c8d063d)

![6](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/17516e68-e1bd-424d-a026-b04b8122a53b)

![7](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/20091bc8-693d-4997-bbe2-2d12fcdd88c5)

Jenkins Getting Started Screen and we can see Jenkins Dashboard.

![8](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/5426b8cf-bb0c-4285-a9c8-b9fa7062634c)

- 2B — Install Docker

```
sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER
sudo chmod 777 /var/run/docker.sock 
sudo docker ps
```
After the docker installation, we create a sonarqube container (Remember added 9000 port in the security group)

Now, Install Sonarqube by executing default sonarqube community image.
```
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```

![9](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/333878b7-0f3c-4390-826a-19edf42c0d7d)

Now, Login to SonarQube by using `<Ec2_Public_IP>:9000`

![10](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/713a9032-1e7f-4321-8c3e-e05b426828df)

Note: By default username and password is `admin` we can change password.

Username : admin 

Password : admin

We can see SonarQube Dashboard:

![11](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/c7ccb865-79f6-466b-aa21-71b675383d0c)

- 2C — Install Trivy

```
sudo apt-get install wget apt-transport-https gnupg lsb-release

wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null

echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list

sudo apt-get update

sudo apt-get install trivy -y
```

Step 3 — Install Plugins like JDK, Sonarqube Scanner, OWASP Dependency Check,

Go to Manage Jenkins → Plugins → Available Plugins →

Install below plugins

1 → Eclipse Temurin Installer (Install without restart)

2 → SonarQube Scanner (Install without restart)

- Go to Manage Jenkins → Plugins → Available Plugins →

![12](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/e63e3e82-e71e-46b2-97fa-05f33942c5f6)

After selecting Plugins, Click on install without restart.

- Configure Java in Global Tool Configuration

Goto Manage Jenkins → Tools → Install JDK17 → Click on Apply and Save

![13](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/ba224683-c5cb-4f76-a8ac-13419da20121)

Step 4 — GotoDashboard → Manage Jenkins → Plugins → OWASP Dependency-Check. Click on it and install without restart.

![14](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/e831b553-38f4-4397-8a50-b046b0a7684f)

First, we configured Plugin and next we have to configure Tool

Goto Dashboard → Manage Jenkins → Tools →

![15](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/2f10790e-eae4-4667-9ca7-c5c457560208)

Click on apply and Save here.

Grab the Public IP Address of your EC2 Instance, Sonarqube works on Port 9000, <EC2_Public_IP>:9000

Goto your Sonarqube Server. Click on Administration → Security → Users → Click on Tokens and Update Token → Give it a name → and click on Generate Token

![16](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/d8c9ca11-f79f-40c8-9aab-019c803e1ec0)

Click on Update Token

![17](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/27270583-e95f-4676-99e8-963199a7453d)

Create a token with a name and generate

 ![18](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/b0573022-e982-4194-abad-cc6f8be48564)

Copy this Token

Go to Jenkins Dashboard → Manage Jenkins → Credentials → Add Secret Text. It should look like this

![19](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/7a15cb6a-2f89-49e7-830f-6f2330dd602b)

You will see this page once you click on create

![20](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/b88afb01-56ca-418b-8c41-9175f41aec2b)

Now, go to Jenkins Dashboard → Manage Jenkins → Configure System

![21](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/4bc23413-4805-409c-88f1-153d1c91ed81)

Click on Apply and Save

The Configure System option is used in Jenkins to configure different server

Global Tool Configuration is used to configure different tools that we install using Plugins

We will install a sonar scanner in the tools.

![22](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/5cd88137-08b2-47f9-af52-f7043c2b78c2)


In the Sonarqube Dashboard add a quality gate also

Administration → Configuration →Webhooks

![23](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/0e5055b8-fe26-4f3d-a76f-6b3e0ba2633f)

Click on Create

![24](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/57dce9fb-e9b6-4cb2-bed9-32b616191fb9)

Add details 

```
# URL Section of Quality Gate
<http://jenkins-public-ip:8080>/sonarqube-webhook
```
After creating Sonar Webhook. we can see below page.

![25](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/6b5937e8-017f-43f9-9050-7ad149aa3ba2)

Step 6 — Create a Pipeline Project in Jenkins using Declarative Pipeline.

Go to Jenkins Dashboard and Create Pipeline Job 

![26](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/7e21132a-7119-4ba5-a236-283b280ae06c)

Add Below Script and Build it.

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
                git branch: 'main', url: 'https://github.com/naveensilver/DotNet-monitoring.git'
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
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
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

Click on Build now, you will see the stage view like this.

![27](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/b3a43f29-2ff0-411e-9bb2-d27e599f4026)

You will see that in status, a graph will also be generated and Vulnerabilities.

![28](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/6bd49f88-bc01-4566-a8bc-b795b9125dd1)

To see the report, you can go to Sonarqube Server and go to Projects.

![29](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/9f335b89-f853-4741-b8d9-85823aafe910)

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

![30](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/54e5104c-036b-4f78-a1b0-1972fe3974ad)

Now, goto Dashboard → Manage Jenkins → Tools →

![31](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/5be02b43-d420-4705-bd3f-e5a94d9f04d7)

Add DockerHub Username and Password under Global Credentials.

![32](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/4c6a92ab-ae90-4856-a842-24487432f339)

In the makefile, we already defined some conditions to build, tag and push images to dockerhub.

![33](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/7e4b3432-5d1f-4761-bf7c-3c5984968f0a)

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
                sh "trivy image naveensilver/dotnet-monitoring:latest > trivy.txt" 
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

![34](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/8c87d479-5b5c-4b9e-8fd6-402301214cfa)

You will see Stage view like this,

![35](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/00f92e98-342e-4e06-bbf1-fff62055c219)

Step 9 — Deploy the image using Docker

Add this stage to your pipeline syntax

```
        stage("Deploy to container"){
            steps{
                sh "docker run -d --name dotnet -p 5000:5000 naveensilver/dotnet-monitoring:latest"
            } 
        }
```

You will see the Stage View like this,

![36](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/137e8de2-f955-4b0e-92cb-46af7198c37d)

(Add port 5000 to Security Group)

And you can access your application on Port 5000. This is a Real World Application that has all Functional Tabs.

Step 10 — Access the Real World Application

![37](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/c4c04e2b-18c4-4f01-9171-b920b86086bf)

![38](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/9a46d65a-a2d7-43c4-90cb-e6bb07c2bc1b)

Step 11 — Kubernetes Set Up

Take-Two Ubuntu 20.04 instances one for k8s master and the other one for worker.

Install Kubectl on Jenkins machine as well.

```
sudo apt update
sudo apt install curl
curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
```

To Install Kubernetes Cluster on Ubuntu 22.04 (Master & Slave) 

Visit >> https://www.linuxtechi.com/install-kubernetes-on-ubuntu-22-04/

After installing Kuberneter Cluster, 

Go to Master Node

```
cd .kube/
cat config
```
Copy the config file to Jenkins master or the local file manager and save it.

[Here, i am saving it in Local file manager i.e., k8s.txt]

Now, Install Kubernetes Plugins

![41](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/094fe5c7-1496-4864-9829-937f30a9d397)

Once it’s installed successfully. 

Go to manage Jenkins → manage credentials → Click on Jenkins global → add credentials

![40](https://github.com/naveensilver/DevSecOps_CI-CD_Project/assets/120022254/e008c037-ae51-4c42-955d-3611ac274270)

The final step to deploy on the Kubernetes cluster, add this stage to the pipeline.

```
stage('Deploy to k8s'){
            steps{
                dir('K8S') {
                  withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                    sh 'kubectl apply -f deployment.yaml'    
                   }
                }   
            }
        }
```

Before starting a new build remove Old containers. `docker rm <contName>`

Output 

```
kubectl get svc
#copy service port 
<worker-ip:svc port>
```

Check the Output in All the servers.

Step 12 — Terminate the AWS EC2 Instance

Lastly, do not forget to terminate the AWS EC2 Instance.


### Final Pipeline 

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
                git branch: 'main', url: 'https://github.com/naveensilver/DotNet-monitoring.git'
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
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
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
                sh "trivy image naveensilver/dotnet-monitoring:latest > trivy.txt" 
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
        stage("Deploy to container"){
            steps{
                sh "docker run -d --name dotnet -p 5000:5000 naveensilver/dotnet-monitoring:latest"
            } 
        }
        stage('Deploy to k8s'){
            steps{
                dir('K8S') {
                  withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                    sh 'kubectl apply -f deployment.yaml'    
                   }
                }   
            }
        }
    }
}
```
### Explanation of Pipeline Stages:

The Given declarative Jenkins Pipeline script written in Groovy, which defines a continuous delivery pipeline for a .NET web application. Let's break down the script step by step:

1. **Agent and Tools:**
   - The pipeline is configured to run on any available agent and uses JDK 17 as the specified tool.

2. **Environment:**
   - The `SCANNER_HOME` environment variable is set to the location of the Sonar Scanner tool using the `tool` directive.

3. **Stages:**
   - **Clean Workspace:** This stage cleans the workspace before the build, ensuring a fresh start for the pipeline.
   - **Checkout From Git:** The pipeline checks out the source code from the specified Git repository (`https://github.com/naveensilver/DotNet-monitoring.git`).
   - **Sonarqube Analysis:** This stage performs a SonarQube analysis on the codebase using the configured SonarQube server and scanner. It sets project name and key for the analysis.
   - **Quality Gate:** The code waits for the SonarQube quality gate status, allowing the pipeline to continue or abort based on the quality gate result.
   - **TRIVY File Scan:** Trivy is used to scan the filesystem for vulnerabilities, and the results are saved to a report file.
   - **OWASP Dependency Check:** This stage performs a security check for project dependencies using OWASP Dependency Check, generating a report in XML format.
   - **Docker Build & Tag:** The pipeline builds and tags a Docker image using the specified credentials and `make image` command.
   - **TRIVY:** Trivy is used to scan the Docker image for vulnerabilities, and the results are saved to a text file named `trivy.txt`.
   - **Docker Push:** The Docker image is pushed to the specified Docker registry using the credentials and `make push` command.
   - **Deploy to Container:** The Docker image is run as a container (named `dotnet`) on port 5000.
   - **Deploy to k8s:** The pipeline deploys the Kubernetes manifest described in the `deployment.yaml` file to a Kubernetes cluster.

Overall, the pipeline automates the entire process of building, testing, and deploying the .NET web application, integrating security checks and containerization for efficient continuous delivery.  

Here is the GitHub for this project : https://github.com/naveensilver/DotNet-monitoring.git

Hope this blog was useful. If yes, please follow me for more content on DevOps. Thank you.

[Project Source : https://medium.com/aws-in-plain-english/real-time-devsecops-pipeline-for-a-dotnet-web-app-ece99ba14219]
