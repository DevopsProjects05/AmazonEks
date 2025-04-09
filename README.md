# Deploy Web Application on Amazon EKS

This guide explains how to deploy a Dockerized Node.js web application to an Amazon EKS cluster using `eksctl`.

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

###  Create and Attach an IAM Role with AdministratorAccess
Follow the steps below to create and attach an IAM role with AdministratorAccess (for practice purposes):

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
