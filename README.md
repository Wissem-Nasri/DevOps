# Project-DevOps
## Description
In this DevOps project, I designed and implemented a robust CI/CD pipeline to streamline the development and deployment of applications in a virtualized environment. Using GitHub as the source code management platform, I automated the integration and delivery processes with Jenkins, ensuring consistent builds and testing. Applications were containerized using Docker and published to DockerHub for easy deployment. For orchestration, Kubernetes was employed to manage containerized workloads, while ArgoCD facilitated continuous delivery with GitOps principles. Code quality and security were maintained through SonarQube for static code analysis and Trivy for container vulnerability scanning. Maven was used to build and manage project dependencies, ensuring reliable and efficient builds. Finally, the application was deployed on virtual machines, simulating a real-world environment to ensure scalability, reliability, and performance. This project highlights expertise in automating workflows, enhancing code quality, and deploying secure, scalable solutions.
![image](https://github.com/user-attachments/assets/e18256b3-5ee1-434e-915c-ce8512f4b4d9)
# Jenkins Master-Agent Setup

Ce dépôt documente la configuration et l'intégration d'une architecture Jenkins comprenant :
- Un serveur **Jenkins Master** pour la gestion et l'orchestration des pipelines CI/CD.
- Un nœud **Jenkins Agent** configuré avec Docker pour exécuter les tâches de build et déploiement.

![image](https://github.com/user-attachments/assets/d234230a-bc9c-48f7-8752-edf19159a0e7)

## Fonctionnalités
- Authentification sécurisée par clé SSH entre Master et Agent.
  
 ![image](https://github.com/user-attachments/assets/9bb8f6e0-18f5-4668-a9ad-bdcd899f6834)

- Configuration de Jenkins pour désactiver le Master comme nœud de build.

![image](https://github.com/user-attachments/assets/730d4575-322f-4f0e-bc54-0b50edc35fb2)

![image](https://github.com/user-attachments/assets/d0838776-0166-4052-882b-f2025e40617d)

- Support pour Docker sur l'Agent pour exécuter des tâches dans des conteneurs.

![image](https://github.com/user-attachments/assets/2d981b93-c806-4c52-b3a9-7ba81fef6a8b)


## Objectif
Automatiser les workflows CI/CD tout en déléguant les exécutions de tâches à des nœuds distants pour une meilleure scalabilité.
## nginx reverse proxy for jenkins 
![image](https://github.com/user-attachments/assets/80c4fc21-755b-488c-b3c3-ca6589bd77cd)
![image](https://github.com/user-attachments/assets/fdb09aac-bf22-44ef-a03c-57e7c5e55e9d)
## Créer un certificat SSL auto-signé et accéder via HTTPS pour un accès sécurisé
$ sudo openssl req -x509 -newkey rsa:4096 -keyout /etc/ssl/private/jenkins.local.key -out /etc/ssl/certs/jenkins.local.crt -days 365 -nodes -subj "/CN=jenkins.local"


![image](https://github.com/user-attachments/assets/a29602f0-4917-4bf9-9e0e-0df6e9a99af4)
![image](https://github.com/user-attachments/assets/7147638d-f529-438f-be00-180428958c72)
![image](https://github.com/user-attachments/assets/791912ea-1a5e-4989-bc3f-504e4f366501)
## tester fonctionnemet d'un pipline
![image](https://github.com/user-attachments/assets/ce34c72f-1c78-46e0-8fbd-93e12cd936bb)

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

## Watch log files and monitor for startup
     $ sudo tail -f /opt/sonarqube/logs/sonar.log


