# Deploy E-commerce Application on Amazon EKS 

This guide explains how to deploy a Dockerized Node.js web application to an Amazon EKS cluster using `eksctl`.

### Architecture 
---

<p align="center">
  <img src="/Images/k8s.png" alt="Kubernetes Architecture" />
</p>


---

## 🚀 Steps to Deploy

### Launch and Prepare EC2 Admin Instance

#### Creating an EC2 Instance:

1. Log in to the AWS Management Console.
2. Navigate to **EC2 > Instances > Launch Instances**.
3. Configure the instance:
   - **AMI**: Amazon Linux 2.
   - **Instance Type**: `t2.medium`.
   - **Create a key pair**
   - **Security Group**: 
     -  Port 22 - Allow SSH 
     -  Port 8080 -HTTP (Optional)
4. Launch the instance and wait for it to initialize.
5. Access the instance and get sudo priviliages: 
   ```bash
   sudo su -
   ```
   Update the instance:
   ```bash
   yum update -y
   ```

#### Create IAM Role with AdministratorAccess
Follow the steps below to create an IAM role with AdministratorAccess (for practice purposes):

**🛠️ Create IAM Role**

- Go to the **AWS Console**, search for **IAM** in the search bar, and open it.

- In the left menu, click on **Roles**.

- Click on **Create role**.

- Select **Custom trust policy**.

Paste the following JSON into the trust policy editor:

```bash
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "eks.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

- Click **Next**.

- Search and select **AdministratorAccess** policy (since this is for practice).

- Click **Next**.

- Enter a role name (e.g., `AWS-EKS`).

- Click **Create role**.

#### Attach IAM Role to EC2 Instance

1. Go to the EC2 Dashboard → Instances.
2. Select your EC2 instance.
3. Click on **Actions → Security → Modify IAM Role**.
4. Choose the role you created (e.g., `AWS-EKS`) and click **Update IAM Role**.



#### Install the required tools:

**Install Eksctl**

```bash
curl -LO "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz"
```
```bash
tar -xzf eksctl_*.tar.gz
```
```bash
sudo mv eksctl /usr/local/bin
```
```bash
eksctl version
```
**Install Kubectl**
```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
```
```bash
chmod +x kubectl
```
```bash
sudo mv kubectl /usr/local/bin
```
```bash
kubectl version --short --client
```
**Install Aws cli**
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```
```bash
unzip awscliv2.zip
```

```bash
sudo yum install -y aws-cli
```
```bash
aws --version
```
**Install git**
```bash
yum install git -y
```
#### Create cluster by using below command
```bash
eksctl create cluster --name My-cluster --region us-east-1 --node-type t2.medium --zones us-east-1a,us-east-1b
```
- **Sit back** and relax for **10 minutes** until the cluster gets created.
  
  
#### You will see the screen below once you execute the above command:

  ---

![](/Images/Cluster%20.jpg)

---

### ✅ Deploy Application on EKS Cluster

Once the EKS cluster is successfully created, follow the steps below:

---

#### 🔁 Clone Deployment and Service Files

```bash
git clone https://github.com/DevopsProjects05/AmazonEks.git
```

### Apply Deployment and Service Manifests
Run the following commands:

```bash
kubectl apply -f /root/AmazonEks/Deployment/deployment.yaml
```
You will see:
```bash
deployment.apps/sample-ecommerce-nodejs-app created
```
```bash
kubectl apply -f /root/AmazonEks/Service/service.yaml
```
You will see:
```bash
service/sample-ecommerce-nodejs-app-service created
```

#### 🔍 Verify Pods

```bash
kubectl get pods
```
You should see output similar to:
```
NAME                                           READY   STATUS    RESTARTS   AGE
sample-ecommerce-nodejs-app-76d5bcf8df-57qr7   1/1     Running   0          28s
sample-ecommerce-nodejs-app-76d5bcf8df-vwvr6   1/1     Running   0          28s
```
#### 🔍 Verify Service

```bash
kubectl get service
```
Output:
```
NAME                                  TYPE           CLUSTER-IP     EXTERNAL-IP                                                              PORT(S)        AGE
kubernetes                            ClusterIP      10.100.0.1     <none>                                                                   443/TCP        34m
sample-ecommerce-nodejs-app-service   LoadBalancer   10.100.72.83   ae0dc6d30e7ac4460ad1865cc4bfeddf-814200144.us-east-1.elb.amazonaws.com   80:30447/TCP   17s
```

#### 📖 Describe Service
To know more details:
```bash
kubectl describe service sample-ecommerce-nodejs-app-service
```

#### Below is the screenshot for your reference of all the commands executed:

---

![](/Images/Cluster2.jpg)

---

#### 🌐 Access the Application

Copy the External-IP of the LoadBalancer, e.g.:
```bash
ae0dc6d30e7ac4460ad1865cc4bfeddf-814200144.us-east-1.elb.amazonaws.com
```
Paste it into your browser. 

#### You will see the deployed eCommerce application:

---

![](/Images/result.jpg)

---

#### 🗑️ Delete the Cluster Resources

To delete the deployment:
```bash
kubectl delete deploy sample-ecommerce-nodejs-app
```
To delete the service:
```bash
kubectl delete service sample-ecommerce-nodejs-app-service
```


#### 🔄 Cluster Deletion with eksctl
completely clean up
```bash
eksctl delete cluster --name My-cluster --region us-east-1
```
✅ It ensures full cleanup of resources (including nodes, control plane, VPC etc.).




#### Below is the screenshot for your reference while deleting:

---
![](/Images/cluster%20delete.jpg)

---


