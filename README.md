# Kubernetes WordPress + MySQL Deployment

This project demonstrates how to deploy **WordPress** with a **MySQL** backend on Kubernetes.  
It uses **Persistent Volumes (PVCs)** for data storage, **Secrets** for sensitive data, and a **LoadBalancer Service** to expose WordPress.

<img width="1659" height="738" alt="Screenshot 2025-10-04 150957" src="https://github.com/user-attachments/assets/3c8da79e-36b1-4653-9252-39f35f11cc9c" />


## 📂 Project Structure
```bash
k8s-wordpress-mysql/
├── mysql
│ ├── mysql-deploy.yml # MySQL Deployment
│ ├── pvc.yml # MySQL PersistentVolumeClaim
│ ├── sc.yml # MySQL StorageClass
│ ├── secret.yml # MySQL Credentials Secret
│ └── svc.yml # MySQL Service (ClusterIP)
├── wordpress
│ ├── deploy.yml # WordPress Deployment
│ ├── pvc-wordpress.yml # WordPress PersistentVolumeClaim
│ ├── sc-wordpress.yml # WordPress StorageClass
│ ├── secret-wp.yml # WordPress Database Credentials Secret
│ └── svc-lb.yml # WordPress Service (LoadBalancer)
└── README.md
```
## 🏗️ Architecture
-----------------------
- **MySQL** runs inside the cluster with its own PVC for data persistence.
- csi driver for EBS and EFS 
- **WordPress** connects to MySQL using credentials stored in a Secret.  
- **WordPress** is exposed externally using a `LoadBalancer` service.  
- **StorageClasses** are provided to dynamically provision storage for both MySQL and WordPress.  
```bash
[ User ] ⇄ [ LoadBalancer Service ] ⇄ [ WordPress Pod ] ⇄ [ MySQL Service ] ⇄ [ MySQL Pod + PVC ]
```
## 🛠️ Technologies Used
------------------------
- **Kubernetes** (Cluster created using `kubeadm`) on EC2--AWS--1 Master and 2 Worker
- **YAML Manifests** (for Deployments, Services, PVCs, StorageClasses, and Secrets)  
- **WordPress** (running as a Deployment)  
- **MySQL** (running as a Deployment with persistent storage EBS)  
- **Persistent Volumes / StorageClass** (for dynamic storage provisioning)  
- **Secrets** (for managing sensitive data like DB credentials)  
- **LoadBalancer Service** (to expose WordPress to the outside world)  
--------------------------------------------------------------------
## 🚀 Deployment Steps
-------------------------------
### 1. Clone the repository
```bash
git clone https://github.com/Mahmosallam/k8s-wordpress-mysql.git
cd k8s-wordpress-mysql
```
2. Deploy MySQL
```bash
kubectl create ns mysql-db
kubectl apply -f mysql/sc.yml
kubectl apply -f mysql/pvc.yml
kubectl apply -f mysql/secret.yml
kubectl apply -f mysql/mysql-deploy.yml
kubectl apply -f mysql/svc.yml
```
## 💾 File System notice
------------------------------
- Create a **File System** (AWS EFS) for persistent storage.  
- Note down the **File System ID** and use it in the StorageClass for WordPress.  
- ------------------------------------
3. Deploy WordPress
```bash
kubectl create ns wordpress-site
kubectl apply -f wordpress/sc-wordpress.yml
kubectl apply -f wordpress/pvc-wordpress.yml
kubectl apply -f wordpress/secret-wp.yml
kubectl apply -f wordpress/deploy.yml
kubectl apply -f wordpress/svc-lb.yml
```
🔍 Verify Deployment
Check that all objects are running:
```bash
kubectl get all -n wordpress-site -o wide
kubectl get all -n mysql-db -o wide
kubectl get sc
kubectl get pv
```
and it will be like:
-----------------------
<img width="1235" height="589" alt="Screenshot 2025-10-04 113835" src="https://github.com/user-attachments/assets/02427cd1-7aad-43ba-8fb1-70b097c612d5" />

## 🌐 Load Balancer & Target Group
------------------------------------
- Created a **Kubernetes LoadBalancer Service** for WordPress.  
- In **AWS**, created a **Load Balancer** and a **Target Group** pointing to the WordPress service nodes.  
- Ensure the port in the LB matches the service port.  
- Traffic flow:
  [ User ] ⇄ [ LoadBalancer Service ] ⇄ [ WordPress Pod ] ⇄ [ MySQL Service ] ⇄ [ MySQL Pod + PVC ]
--------------------------------------------------------------------------------
# Notes
---------------
- Use Namespaces (wordpress-site and mysql-db) to organize resources.
- Update Secrets to use your own database credentials.
- Dont forget csi drivers
- On Minikube, use minikube service wordpress-service --url to access WordPress.
--------------------------------------------------------------------------------
this is to verify my work:
-----------------------------
![chrome_9XCcwEEdAe](https://github.com/user-attachments/assets/1c07fb57-47d0-4345-90ba-f4a3b671f0a3)

---------------------------------------------
and verify database is work:
-----------------------------
https://github.com/user-attachments/assets/442e4b92-9fef-4712-88ff-429c90656768
``` bash
and finally This project is for educational purposes. Feel free to fork and modify.
```








