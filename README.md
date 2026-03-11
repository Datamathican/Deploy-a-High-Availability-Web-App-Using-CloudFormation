#  Udagram: Deploy a High-Availability Web App using CloudFormation

![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![CloudFormation](https://img.shields.io/badge/CloudFormation-FF4F8B?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)

**Author:** Pramod Singh  
**Date:** March 10, 2026

**Live Application URL:** `http://udagra-webap-2zysdeqgci3f-1890842633.us-east-1.elb.amazonaws.com`

---

## 📝 Project Overview

This project automates the deployment of a **high-availability web application** using **AWS CloudFormation**. The infrastructure is designed to be resilient, secure, and scalable, strictly adhering to AWS best practices. The application is hosted on Ubuntu EC2 instances within a private network and is securely accessible to the public via an Application Load Balancer (ALB).

---

## 🏗️ Architecture Diagram

The diagram below illustrates the complete cloud infrastructure, including the VPC, Public/Private Subnets, NAT Gateways, Load Balancers, Auto Scaling Group, and S3/CloudFront integrations.

![Architecture Diagram](Infrastructure%20Diagram.png)
---

## ⚙️ Infrastructure Components

### 1. 🌐 Network Layer (`network.yml`)
* **VPC:** A Virtual Private Cloud configured with a custom CIDR block.
* **Subnets:** 4 Subnets (2 Public and 2 Private) spread evenly across two Availability Zones for high availability.
* **Internet Gateway:** Provides internet access for the public subnets.
* **NAT Gateways:** Two NAT Gateways (one in each AZ) to allow private instances to access the internet for necessary security updates while remaining inaccessible from the outside world.

### 2. 🖥️ Application Layer (`udagram.yml`)
* **Launch Template:** Configured with **Ubuntu 22.04 LTS**, a **t2.micro** instance type, and a **10GB** EBS volume.
* **Auto Scaling Group (ASG):** Maintains a minimum of **4 EC2 instances** running concurrently across the private subnets.
* **Application Load Balancer (ALB):** Distributed across the public subnets to efficiently route incoming HTTP traffic to the private EC2 instances.
* **Security Groups:** * **ALB Security Group:** Allows Port 80 (HTTP) traffic from anywhere (`0.0.0.0/0`).
  * **Web Server Security Group:** Only allows Port 80 (HTTP) traffic originating *specifically* from the Load Balancer, enforcing the principle of least privilege.
* **IAM Role:** An Instance Profile allowing EC2 instances read-only access to download application files from an S3 bucket.

### 3. 🚀 Bonus Content (Static Web & CDN)
* **S3 Bucket:** Provisioned to securely store static web assets.
* **CloudFront Distribution:** A Content Delivery Network (CDN) to serve the static content globally with ultra-low latency.

---

## 🛠️ Getting Started

### Prerequisites
* An active **AWS Account**.
* **AWS CLI** installed and configured with your credentials.
* A bash terminal (AWS CloudShell is highly recommended).

### Deployment Steps

**1. Clone the Repository:**
```bash
git clone [https://github.com/Datamathican/Deploy-a-High-Availability-Web-App-using-CloudFormation.git](https://github.com/Datamathican/Deploy-a-High-Availability-Web-App-using-CloudFormation.git)
cd Deploy-a-High-Availability-Web-App-using-CloudFormation
```
**2. Deploy the Network Stack:**

```bash
./create.sh UdagramNetwork infrastructure/network.yml infrastructure/network-parameters.json
Wait for the status to show CREATE_COMPLETE in the AWS CloudFormation Console before moving on.
```
**3. Deploy the Application Stack:**

```bash
./create.sh UdagramApp infrastructure/udagram.yml infrastructure/udagram-parameters.json
```
**4. Access the Application:**
Once the stack deployment is fully complete, navigate to the Outputs tab of the UdagramApp stack in the AWS Console and click the LoadBalancerURL.


To avoid ongoing AWS charges, it is crucial to delete the resources when you are done testing. You must delete the application stack first, as it depends on the network stack.

```bash
# 1. Delete the Application Stack
./delete.sh UdagramApp
```
```bash
# 2. Delete the Network Stack
./delete.sh UdagramNetwork
```
**🎓 License**
This project was developed as part of the Udacity AWS Cloud DevOps Engineer Nanodegree.
