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


