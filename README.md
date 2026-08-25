# Student ERP on Kubernetes

## Project Overview

In this project, we deploy **Student ERP Application on Kubernetes**.

The deployment contains:

```text
K8s Cluster
    |
    |-------------------------
    |                        |
 Backend                  Frontend
    |                        |
    |                        |
    ----------- DB ----------
             |
          RDS MariaDB
```

---

# Deployment Steps

The complete setup is done in the following order:

1. Create K8s Cluster
2. Create Nodes
3. Setup RDS DB
4. Create EC2 Instance to Access Cluster
5. Install K8s and AWS CLI
6. Login into Cluster
7. Check Nodes
8. Clone GitHub Repository
9. Setup Database
10. Setup Backend
11. Setup Frontend
12. Access Application using Load Balancer DNS

---

# 1. Create K8s Cluster

1st of all create a **K8s Cluster**.

When the cluster comes in **Active Mode**, create nodes.

```text
Create K8s Cluster
        |
        v
Cluster Active
        |
        v
Create Nodes
```

---

# 2. Create Nodes

When the K8s Cluster is active, create the required nodes.

Till nodes are created, setup the RDS DB.

---

# 3. Setup RDS DB

Create **RDS Database (MariaDB)** with:

```text
Full Configuration
```

Keep the required database configuration ready.

---

# 4. Create EC2 Instance

Create an EC2 instance to access the K8s Cluster from outside.

```text
K8s Cluster
     |
     |
     v
EC2 Instance
     |
     v
Access Cluster from Outside
```

---

# 5. Install K8s and AWS CLI

After the cluster is created, install:

* Kubernetes
* AWS CLI

on the EC2 instance.

---

# 6. Login into K8s Cluster

Login into the K8s Cluster from the EC2 instance.

After login, check whether nodes are ready or not.

Check nodes:

```bash
kubectl get nodes
```

Nodes should be in the required ready state.

---

# 7. Clone GitHub Repository

Clone the GitHub repository on the EC2 instance.

```bash
git clone <GitHub-Repository-Link>
```

After cloning, the repository is available on the EC2 instance.

---

# 8. Database Setup

Now setup the database.

## Install MySQL Client

On the EC2 instance:

```bash
apt update && apt install mysql-client -y
```

---

## Login into MySQL DB

Connect to the RDS MariaDB database:

```bash
mysql -h <DB_end-pt> -u <username> -p
```

Enter the database password.

---

## Create Database

```sql
CREATE DATABASE student_db;
```

---

## Create User and Give Privileges

Create a user and give all privileges to them:

```sql
GRANT ALL PRIVILEGES ON springbackend.* TO 'username'@'localhost' IDENTIFIED BY 'your_password';
```

---

## Use Database

```sql
USE student_db;
```

---

## Create Table

Create the `students` table:

```sql
CREATE TABLE `students` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  `email` varchar(255) DEFAULT NULL,
  `course` varchar(255) DEFAULT NULL,
  `student_class` varchar(255) DEFAULT NULL,
  `percentage` double DEFAULT NULL,
  `branch` varchar(255) DEFAULT NULL,
  `mobile_number` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=80 DEFAULT CHARSET=latin1 COLLATE=latin1_swedish_ci;
```

---

## Exit from DB

```sql
exit;
```

DB setup is ready.

---

# 9. Backend Setup

## Go Inside Repository

Go to the Backend folder:

```bash
cd EasyCRUD-Updated/backend
```

---

## Connect Backend to DB

Edit the Backend configuration file:

```bash
nano src/main/resources/application.properties
```

Paste the:

```text
DB_end-pt
```

here.

Check the database details properly.

---

# 10. Install Docker

Install Docker on the EC2 instance:

```bash
apt update && apt install docker.io -y
```

---

# 11. Login into DockerHub

Login into DockerHub:

```bash
docker login -u <username>
```

Enter the required DockerHub password/token.

---

# 12. Build Backend Docker Image

Build the Docker Image for Backend resources:

```bash
docker build . -t <username/imagename:tag>
```

Example format:

```text
username/imagename:tag
```

---

# 13. Push Backend Image to DockerHub

Push the Docker Image to private DockerHub:

```bash
docker push <username/imagename:tag>
```

---

# 14. Edit Backend Pod / Deployment File

Now edit the `pod.yaml` file (Backend deployment file).

```bash
nano <filename>
```

Call the Docker Image which we pushed to DockerHub.

Change the Image Name in the deployment file.

```text
image: <username/imagename:tag>
```

---

# 15. Create / Run Backend Pod

Create/run the Pod:

```bash
kubectl apply -f <filename>
```

---

# 16. Check Backend Pod

Check whether the Pod is created/running or not:

```bash
kubectl get pods
```

---

# 17. Create K8s Service for Backend

Create a Kubernetes Service to expose Backend:

```bash
kubectl apply -f <filename>
```

---

# 18. Check Backend Service

Check whether the Service is created or not:

```bash
kubectl get svc
```

Backend setup is done.

---

# 19. Frontend Setup

Now setup the Frontend.

Go to the Frontend folder:

```bash
cd EasyCRUD-Updated/frontend
```

---

# 20. Install Docker if Needed

Install Docker if it is not already installed.

```bash
apt update && apt install docker.io -y
```

---

# 21. Connect Backend with Frontend

Now connect Backend with Frontend.

First check the Backend Service:

```bash
kubectl get svc
```

Copy the Backend Service endpoint from here.

---

# 22. Add Backend Endpoint to Frontend

Edit the `.env` file:

```bash
nano .env
```

Paste the Backend Service endpoint into the `.env` file for connection.

```text
Backend Service Endpoint
        |
        v
Frontend .env
```

---

# 23. Build Frontend Docker Image

Here do the same step as done in Backend setup.

Build the Docker Image:

```bash
docker build . -t <username/imagename:tag>
```

---

# 24. Push Frontend Image to DockerHub

Push the Frontend Docker Image:

```bash
docker push <username/imagename:tag>
```

---

# 25. Call Image in Deployment File

Edit the Frontend deployment file:

```bash
nano <filename>
```

Change the Image Name to the Docker Image which we pushed to DockerHub.

```text
image: <username/imagename:tag>
```

---

# 26. Create / Run Frontend Pod

Create/run the Frontend Pod:

```bash
kubectl apply -f <filename>
```

---

# 27. Create Frontend Service

Create the Kubernetes Service:

```bash
kubectl apply -f <filename>
```

Check the Service:

```bash
kubectl get svc
```

Finally Frontend is also done.

---

# 28. Access Student ERP Application

Now copy the **DNS of the Load Balancer Service**.

Paste it into the browser.

```text
Load Balancer DNS
        |
        v
     Browser
        |
        v
Student ERP Application
```

We will see our **Student ERP Application**.

---

# 29. If Application is Not Accessible

If it is not possible to access the application, check the **Security Group of Load Balancer** once.

Make sure the required traffic is allowed.

For this setup:

```text
Security Group
      |
      v
Allow All Traffic
```

---

# 30. Why We Copy DNS of Load Balancer?

We copy the DNS of the Load Balancer because we use the **Load Balancer Service** in the Pod Deployment.

The Load Balancer provides the endpoint through which we access the application.

```text
Browser
   |
   v
Load Balancer DNS
   |
   v
Load Balancer Service
   |
   v
Frontend Pod
   |
   v
Backend Service
   |
   v
Backend Pod
   |
   v
RDS MariaDB
```

---

# 31. Complete Student ERP Kubernetes Flow

```text
                    User
                     |
                     v
             Load Balancer DNS
                     |
                     v
              Load Balancer
                     |
                     v
               Frontend Pod
                     |
                     v
             Backend Service
                     |
                     v
               Backend Pod
                     |
                     v
                RDS MariaDB
```

---

# 32. Complete Deployment Flow

```text
1. Create K8s Cluster
          |
          v
2. Cluster Active
          |
          v
3. Create Nodes
          |
          v
4. Setup RDS MariaDB
          |
          v
5. Create EC2 Access Instance
          |
          v
6. Install K8s + AWS CLI
          |
          v
7. Login into Cluster
          |
          v
8. Check Nodes
          |
          v
9. Clone GitHub Repository
          |
          v
10. Setup DB
          |
          v
11. Setup Backend
          |
          v
12. Build Backend Docker Image
          |
          v
13. Push Image to DockerHub
          |
          v
14. Create Backend Pod
          |
          v
15. Create Backend Service
          |
          v
16. Setup Frontend
          |
          v
17. Connect Frontend with Backend
          |
          v
18. Build Frontend Docker Image
          |
          v
19. Push Image to DockerHub
          |
          v
20. Create Frontend Pod
          |
          v
21. Create Frontend Service
          |
          v
22. Copy Load Balancer DNS
          |
          v
23. Open DNS in Browser
          |
          v
24. Student ERP Application
```

---

# 33. Important Points

* First create the K8s Cluster.
* When the Cluster becomes active, create Nodes.
* RDS MariaDB is used as the Database.
* EC2 instance is used to access the K8s Cluster from outside.
* Install K8s and AWS CLI on the EC2 access instance.
* Login into the Cluster and check Nodes.
* Clone the GitHub repository.
* Setup the Database before Backend.
* Backend is connected with the RDS DB Endpoint.
* Docker is used to build Backend and Frontend Images.
* DockerHub is used to store the Images.
* The Images are called inside the Kubernetes deployment files.
* Kubernetes Pods run the Backend and Frontend.
* Kubernetes Services are created to expose the application components.
* Frontend is connected with Backend using the Backend Service Endpoint.
* Load Balancer Service is used for accessing the application.
* Load Balancer DNS is copied and opened in the Browser.
* If the application is not accessible, check the Load Balancer Security Group.

---

# 34. Project Summary

In this project, we deploy **Student ERP on Kubernetes**.

We use:

```text
AWS EKS / K8s
      |
      |---- Worker Nodes
      |
      |---- Backend Pod
      |
      |---- Frontend Pod
      |
      |---- Kubernetes Services
      |
      |---- Load Balancer
      |
      v
RDS MariaDB
```

The complete application is deployed using:

* Kubernetes
* Docker
* DockerHub
* AWS EC2
* AWS RDS MariaDB
* Load Balancer

Finally, we copy the **Load Balancer DNS**, paste it into the browser, and access the **Student ERP Application**.
