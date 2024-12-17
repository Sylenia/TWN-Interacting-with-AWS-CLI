# Demo Project: Interacting with AWS CLI

## **Project Overview**
This project demonstrates how to interact with AWS services using the AWS Command Line Interface (CLI). The project involves setting up the AWS CLI tool, creating and managing AWS resources like EC2 instances, Security Groups, and IAM users, and listing resources efficiently. 

---

## **Technologies Used**
- **AWS**: For cloud infrastructure.
- **Linux**: For command-line operations.

---

## **1. Install and Configure AWS CLI**

### **Step 1: Install AWS CLI**
Run the following commands to install AWS CLI on your system:

#### **On Linux**
```bash
# Download AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

# Unzip the installer
unzip awscliv2.zip

# Run the installer
sudo ./aws/install

# Verify installation
aws --version
```

#### **On Windows**
1. Download the MSI installer from the [AWS CLI Official Page](https://aws.amazon.com/cli/).
2. Follow the on-screen instructions to install the AWS CLI.
3. Verify installation:
   ```cmd
   aws --version
   ```

---

### **Step 2: Configure AWS CLI for Your Device**
Run the following command to set up the AWS CLI:

```bash
aws configure
```
You will be prompted to enter:
- **AWS Access Key ID**
- **AWS Secret Access Key**
- **Default region name** (e.g., `us-east-1`)
- **Default output format** (e.g., `json`)

Alternatively, you can set credentials using environment variables:
```bash
export AWS_ACCESS_KEY_ID="YOUR_ACCESS_KEY_ID"
export AWS_SECRET_ACCESS_KEY="YOUR_SECRET_ACCESS_KEY"
```

---

### **Step 3: Create an SSH Key Pair**
To create a key pair for accessing EC2 instances, run:
```bash
aws ec2 create-key-pair --key-name "YOUR_KEY_NAME" --query "KeyMaterial" --output text > "YOUR_KEY_NAME.pem"
```
Set the correct permissions for the key file:
```bash
chmod 400 YOUR_KEY_NAME.pem
```

---

## **2. AWS CLI Commands for EC2 Management**

### **Describe Resources**
- **Describe VPCs**:
  ```bash
  aws ec2 describe-vpcs
  ```
- **Describe Subnets**:
  ```bash
  aws ec2 describe-subnets
  ```
- **Describe Instances**:
  ```bash
  aws ec2 describe-instances
  ```
- **Filter Instances by Specific Criteria**:
  ```bash
  aws ec2 describe-instances --filters Name="ENTER_FILTER_NAME",Values="ENTER_VALUE" --query "Reservations[].Instances[].InstanceId"
  ```

### **Create a Security Group**
```bash
aws ec2 create-security-group --group-name "YOUR_GROUP_NAME" --description "ENTER_DESCRIPTION" --vpc-id "YOUR_VPC_ID"
```

### **Authorize Security Group Ingress**
Allow inbound traffic for specific ports:
```bash
aws ec2 authorize-security-group-ingress --group-id "YOUR_GROUP_ID" --protocol "ENTER_PROTOCOL" --port "YOUR_PORT_NUMBER" --cidr "YOUR_CIDR_BLOCK"
```

### **Run an EC2 Instance**
Launch a new EC2 instance with a key pair and security group:
```bash
aws ec2 run-instances --image-id "YOUR_AMI_ID" --count "1" --instance-type "YOUR_INSTANCE_TYPE" --key-name "YOUR_KEY_NAME" --security-group-ids "YOUR_SECURITY_GROUP_ID" --subnet-id "YOUR_SUBNET_ID"
```

---

## **3. AWS CLI Commands for IAM Management**

### **Create IAM Group and User**
- **Create a Group**:
  ```bash
  aws iam create-group --group-name "YOUR_GROUP_NAME"
  ```
- **Create a User**:
  ```bash
  aws iam create-user --user-name "YOUR_USER_NAME"
  ```
- **Add User to Group**:
  ```bash
  aws iam add-user-to-group --user-name "YOUR_USER_NAME" --group-name "YOUR_GROUP_NAME"
  ```
- **List Group Members**:
  ```bash
  aws iam get-group --group-name "YOUR_GROUP_NAME"
  ```

### **Attach Policies**
- **Attach a Policy to a User**:
  ```bash
  aws iam attach-user-policy --user-name "YOUR_USER_NAME" --policy-arn "YOUR_POLICY_ARN"
  ```
- **List Attached Policies for a Group**:
  ```bash
  aws iam list-attached-group-policies --group-name "YOUR_GROUP_NAME"
  ```

### **Create IAM Policies**
- **Create a Custom Policy**:
  ```bash
  aws iam create-policy --policy-name "YOUR_POLICY_NAME" --policy-document file://YOUR_FILE_NAME
  ```

- **Attach Policy to a Group**:
  ```bash
  aws iam attach-group-policy --group-name "YOUR_GROUP_NAME" --policy-arn "YOUR_POLICY_ARN"
  ```

### **Create Login Profile for IAM User**
Set a password for IAM user login:
```bash
aws iam create-login-profile --user-name "YOUR_USER_NAME" --password "YOUR_PASSWORD" --password-reset-required
```

### **Create Access Key for IAM User**
Generate access keys for programmatic access:
```bash
aws iam create-access-key --user-name "YOUR_USER_NAME"
```

---

## **4. Useful AWS CLI Commands**
- List available regions:
  ```bash
  aws ec2 describe-regions
  ```
- List all running instances:
  ```bash
  aws ec2 describe-instances --query "Reservations[].Instances[].[InstanceId,State.Name]"
  ```
- List IAM Policies by Name:
  ```bash
  aws iam list-policies --query "Policies[?PolicyName=='YOUR_POLICY_NAME'].Arn" --output text
  ```

---

## **5. Bonus Section: Tips and Additional Tools**

### **Automating Commands with Shell Scripts**
Create reusable scripts to manage resources. For example, a script to launch and describe EC2 instances:
```bash
#!/bin/bash
aws ec2 run-instances --image-id "ami-12345678" --count 1 --instance-type "t2.micro" --key-name "my-key" --security-group-ids "sg-12345678"
aws ec2 describe-instances --filters Name=instance-state-name,Values=running
```

### **AWS CLI Profiles**
Use named profiles to manage multiple AWS accounts:
```bash
aws configure --profile my-profile
aws s3 ls --profile my-profile
```

### **AWS CloudFormation**
Leverage CloudFormation to automate infrastructure provisioning:
```bash
aws cloudformation create-stack --stack-name my-stack --template-body file://template.yml
```

### **Additional DevOps Tips**
1. **Automated Log Retrieval**
   Automate retrieval of CloudWatch logs for debugging and monitoring purposes:
   ```bash
   aws logs get-log-events --log-group-name "YOUR_LOG_GROUP" --log-stream-name "YOUR_LOG_STREAM"
   ```
   *Useful for quickly checking application or system logs without accessing the AWS console.*

2. **Infrastructure Drift Detection**
   Use CloudFormation to detect infrastructure drift, ensuring your deployed environment matches the desired configuration:
   ```bash
   aws cloudformation detect-stack-drift --stack-name "my-stack"
   aws cloudformation describe-stack-drift-detection-status --stack-drift-detection-id "YOUR_ID"
   ```
   *Critical for managing infrastructure consistency in automated DevOps pipelines.*

3. **EC2 Instance Tagging for Automation**
   Tag EC2 instances during creation to facilitate automation workflows and resource management:
   ```bash
   aws ec2 create-tags --resources "YOUR_INSTANCE_ID" --tags Key=Environment,Value=Production
   ```
   *Tags help organize and automate resource management based on specific labels.*

### **DevSecOps Security Measures**
1. **IAM Credential Report for Auditing**
   Generate a credential report to audit the use of IAM user credentials, including key rotation and security compliance:
   ```bash
   aws iam generate-credential-report
   aws iam get-credential-report --output text > credential-report.csv
   ```
   *Ensures compliance with security policies and identifies outdated credentials.*

2. **Enforce Multi-Factor Authentication (MFA)**
   Automate the process of enabling MFA for IAM users:
   ```bash
   aws iam create-virtual-mfa-device --virtual-mfa-device-name "YOUR_USER_MFA" --outfile "mfa-qr.png"
   aws iam enable-mfa-device --user-name "YOUR_USER_NAME" --serial-number "arn:aws:iam::ACCOUNT_ID:mfa/YOUR_USER_MFA" --authentication-code1 CODE1 --authentication-code2 CODE2
   ```
   *Enhances account security by enforcing multi-factor authentication for IAM users.*

---

Happy Automating! 🚀
