
# Project-DevOps
## Description
In this DevOps project, I designed and implemented a robust CI/CD pipeline to streamline the development and deployment of applications in a virtualized environment. Using GitHub as the source code management platform, I automated the integration and delivery processes with Jenkins, ensuring consistent builds and testing. Applications were containerized using Docker and published to DockerHub for easy deployment. For orchestration, Kubernetes was employed to manage containerized workloads, while ArgoCD facilitated continuous delivery with GitOps principles. Code quality and security were maintained through SonarQube for static code analysis and Trivy for container vulnerability scanning. Maven was used to build and manage project dependencies, ensuring reliable and efficient builds. Finally, the application was deployed on virtual machines, simulating a real-world environment to ensure scalability, reliability, and performance. This project highlights expertise in automating workflows, enhancing code quality, and deploying secure, scalable solutions.

![Untitled Diagram (21)](https://github.com/user-attachments/assets/6fd7db5f-ead8-464c-a5af-6f9677266e8c)

# Install and Configure the Jenkins-Master & Jenkins-Agent 
$ sudo apt update
$ sudo apt upgrade
$ sudo nano /etc/hostname
$ sudo init 6
$ sudo apt install openjdk-17-jre
$ java -version

## Install Jenkins
Refer--https://www.jenkins.io/doc/book/installing/linux/
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins

$ sudo systemctl enable jenkins      
$ sudo systemctl start jenkins        
$ systemctl status jenkins
$ sudo nano /etc/ssh/sshd_config
$ sudo service sshd reload
$ ssh-keygen OR $ ssh-keygen -t ed25519
$ cd .ssh

![image](https://github.com/user-attachments/assets/38d974f2-b7a0-42c3-98e1-0620e382f60e)

l' architecture Jenkins comprenant :
#### Un serveur Jenkins Master pour la gestion et l'orchestration des pipelines CI/CD.
#### Un nœud Jenkins Agent configuré avec Docker pour exécuter les tâches de build et déploiement.

![image](https://github.com/user-attachments/assets/d234230a-bc9c-48f7-8752-edf19159a0e7)

#### Authentification sécurisée par clé SSH entre Master et Agent.

![image](https://github.com/user-attachments/assets/da98b14d-cb4c-4d9d-92b6-763b4ea01d79)

#### Configuration de Jenkins pour désactiver le Master comme nœud de build.

![image](https://github.com/user-attachments/assets/0646d0b3-9bc0-4a50-ae35-e45cc235cacb)
#### Support pour Docker sur l'Agent pour exécuter des tâches dans des conteneurs.

https://docs.docker.com/engine/install/ubuntu/

![image](https://github.com/user-attachments/assets/296f7cd2-086a-45ce-afd3-f336bf42404b)

# Objectif

Automatiser les workflows CI/CD tout en déléguant les exécutions de tâches à des nœuds distants pour une meilleure scalabilité.


# tester fonctionnemet d'un pipline

![image](https://github.com/user-attachments/assets/ed140752-f293-4d22-87d7-3aa0fbde279e)

# install plugin

## (Dashboard > Manage Jenkins > plugins)
### maven
### eclipse 
### sonar
### docker 
# install tools
## (Dashboard > Manage Jenkins > tools)
### maven 
### jdk installations
### sonarqube scanner installation
## (Dashboard > Manage Jenkins > system)
### Sonarqube server
# credentials 
## github
## jenkins-sonarqube-token
## dockerhub
## JENKINS_API_TOKEN

![image](https://github.com/user-attachments/assets/46e59a95-9987-443e-9892-6b43f20e9d0b)

# configuration de test-app (pipline)
![image](https://github.com/user-attachments/assets/ec867cf2-6db1-4f2b-8be0-70b04a19ef85)

# Install and Configure the SonarQube
## Update Package Repository and Upgrade Packages
    $ sudo apt update
    $ sudo apt upgrade
## Add PostgresSQL repository
    $ sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
    $ wget -qO- https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo tee /etc/apt/trusted.gpg.d/pgdg.asc &>/dev/null
## Install PostgreSQL
    $ sudo apt update
    $ sudo apt-get -y install postgresql postgresql-contrib
    $ sudo systemctl enable postgresql
    ![image](https://github.com/user-attachments/assets/2bde5b52-0bb0-4a32-99c0-49f3c16522b2)

## Create Database for Sonarqube
    $ sudo passwd postgres
    $ su - postgres
    $ createuser sonar
    $ psql 
    $ ALTER USER sonar WITH ENCRYPTED password 'sonar';
    $ CREATE DATABASE sonarqube OWNER sonar;
    $ grant all privileges on DATABASE sonarqube to sonar;
    $ \q
    $ exit
## Add Adoptium repository
    $ sudo bash
    $ wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | tee /etc/apt/keyrings/adoptium.asc
    $ echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list
 ## Install Java 17
    $ apt update
    $ apt install temurin-17-jdk
    $ update-alternatives --config java
    $ /usr/bin/java --version
    $ exit 
## Linux Kernel Tuning
   # Increase Limits
    $ sudo vim /etc/security/limits.conf
    //Paste the below values at the bottom of the file
    sonarqube   -   nofile   65536
    sonarqube   -   nproc    4096

    # Increase Mapped Memory Regions
    sudo vim /etc/sysctl.conf
    //Paste the below values at the bottom of the file
    vm.max_map_count = 262144

#### Sonarqube Installation ####
## Download and Extract
    $ sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.0.65466.zip
    $ sudo apt install unzip
    $ sudo unzip sonarqube-9.9.0.65466.zip -d /opt
    $ sudo mv /opt/sonarqube-9.9.0.65466 /opt/sonarqube
## Create user and set permissions
     $ sudo groupadd sonar
     $ sudo useradd -c "user to run SonarQube" -d /opt/sonarqube -g sonar sonar
     $ sudo chown sonar:sonar /opt/sonarqube -R
## Update Sonarqube properties with DB credentials
     $ sudo vim /opt/sonarqube/conf/sonar.properties
     //Find and replace the below values, you might need to add the sonar.jdbc.url
     sonar.jdbc.username=sonar
     sonar.jdbc.password=sonar
     sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube
## Create service for Sonarqube
$ sudo vim /etc/systemd/system/sonar.service
//Paste the below into the file
     [Unit]
     Description=SonarQube service
     After=syslog.target network.target

     [Service]
     Type=forking

     ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
     ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

     User=sonar
     Group=sonar
     Restart=always

     LimitNOFILE=65536
     LimitNPROC=4096

     [Install]
     WantedBy=multi-user.target

## Start Sonarqube and Enable service
     $ sudo systemctl start sonar
     $ sudo systemctl enable sonar
     $ sudo systemctl status sonar
![image](https://github.com/user-attachments/assets/1823441e-7931-47b5-ab35-d50a5b1c2a14)

## Watch log files and monitor for startup
     $ sudo tail -f /opt/sonarqube/logs/sonar.log
# integrate sonarqube with jenkins 
### generate token 
![image](https://github.com/user-attachments/assets/7764f7e2-1bc9-479b-aa4b-e89ffc58213b)
![image](https://github.com/user-attachments/assets/c5ada1f9-81a7-4b04-9342-24f0d0bdc45d)
### create webhook
![image](https://github.com/user-attachments/assets/3c074dba-fd29-4999-83ba-0a354ff4d8e0)
# build and push docker image using pipline script 
![image](https://github.com/user-attachments/assets/abc34eb8-64ec-49ed-a347-13d8936878e8)
# Setting Up a Kubernetes Cluster with Kubeadm and Containerd
https://www.youtube.com/watch?v=DrcS4jrA_no&t=2113s&pp=ygUTa3ViZXJuZXRlcyBpbnN0YWxsIA%3D%3D
![image](https://github.com/user-attachments/assets/f244eca0-a2e8-4b18-8892-c05bda8243e2)

![image](https://github.com/user-attachments/assets/868d8e88-d7da-4fa9-9232-b5f90987ae1b)

![image](https://github.com/user-attachments/assets/2f0b2e0d-2b85-44a1-b4c8-0e3d0974e2ca)

# ArgoCD Installation on EKS Cluster and Add EKS Cluster to ArgoCD
1 ) First, create a namespace
    $ kubectl create namespace argocd

2 ) Next, let's apply the yaml configuration files for ArgoCd
    $ kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

3 ) Now we can view the pods created in the ArgoCD namespace.

    $ kubectl get pods -n argocd

![image](https://github.com/user-attachments/assets/c0c26238-2eae-4730-8fb1-68e649f27b41)

4 ) To interact with the API Server we need to deploy the CLI:
    $ curl --silent --location -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/v2.4.7/argocd-linux-amd64
    $ chmod +x /usr/local/bin/argocd

5 ) Expose argocd-server
    $ kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}' 


6 ) Wait about 2 minutes for the NodePort creation
    $ kubectl get svc -n argocd

![image](https://github.com/user-attachments/assets/26520221-d8c5-48e9-bffa-869a7d6d6177)


7 ) Get pasword and decode it.

    $ kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

![image](https://github.com/user-attachments/assets/45fc3d98-7752-4560-8d59-526f2f46a188)

![image](https://github.com/user-attachments/assets/fd42688c-820a-4e24-85b5-da35cc36a363)


## Add  Cluster to ArgoCD
9 ) login to ArgoCD from CLI

    $ argocd login https://192.168.122.187:30473 --username admin

10 ) 
     $ argocd cluster list

11 ) Below command will show the  cluster
     $ kubectl config get-contexts

12 ) Add above cluster to ArgoCD with below command
     $ argocd cluster add  kubernetes-admin@kubernetes --name vmware-OwnCluster
![image](https://github.com/user-attachments/assets/e078dbfb-ff36-4159-a05c-3318818d2548)

![image](https://github.com/user-attachments/assets/29dc8a75-3db5-4b21-8756-af906714c611)


13 ) $ kubectl get svc

![image](https://github.com/user-attachments/assets/e4ed8bae-dfbd-4aa0-ac56-31dba7b2deb7)

# configure argocd to Deploy pods on kubernities and automate argocd Deployment Job using Gitops Github Repository 
## create applicaation in cluster 
![image](https://github.com/user-attachments/assets/5d247193-ece2-4a95-a319-ba0eaff8b3c6)
![image](https://github.com/user-attachments/assets/71f4cad9-9308-494e-b851-888c4c6a660d)
![image](https://github.com/user-attachments/assets/e8de6fee-3fdf-4771-9862-ff43497f8fc5)
## gitops-automation 
![image](https://github.com/user-attachments/assets/ea289511-4124-4b49-b716-514a2b6e83a9)
## before making changes in code 
![image](https://github.com/user-attachments/assets/aa7015f1-60d8-4414-ba8a-6f3b3fc5fcf2)

![image](https://github.com/user-attachments/assets/dfbaf8a9-8b91-44fa-bd07-8b2f9c677cf7)

![image](https://github.com/user-attachments/assets/cde966c2-8a4b-486b-bfe1-0a4972a18708)


## after making changes 

![image](https://github.com/user-attachments/assets/bc54d56c-71ba-4c8b-bb36-3efcbd8f5351)

### pipline

![image](https://github.com/user-attachments/assets/0973dc6c-a7f6-4e28-bc44-8c7d3c0647fd)

![image](https://github.com/user-attachments/assets/f0beee56-baea-42d4-a6b6-fe0abc779e90)

#### sonarqube (passed)
![image](https://github.com/user-attachments/assets/08b79c7f-191d-4d9c-9249-e6713400a476)
#### deployment app (current version ) 

![image](https://github.com/user-attachments/assets/0c8d160b-ff0a-4d0e-904e-041987848234)

![image](https://github.com/user-attachments/assets/f69108fa-d905-401c-ad85-e61f906b9d00)

![image](https://github.com/user-attachments/assets/39e88ea8-9ddb-450c-a590-33c933e0adb7)

![image](https://github.com/user-attachments/assets/4487bc25-dd30-4b31-8a0b-b1c99dcb9369)


