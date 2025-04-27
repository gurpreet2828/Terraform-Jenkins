# Deploying Jenkins on AWS using Terraform, Ansible & Ubuntu

## Summary

**Objective:** The main objective of this project was to gain hands-on experience with cloud infrastructure provisioning, automation, and CI/CD pipeline setup. By deploying Jenkins on an AWS EC2 instance using Terraform and Docker, I aimed to learn how to automate cloud resource management (Infrastructure as Code), install and configure DevOps tools in a scalable environment, and troubleshoot real-world deployment challenges. This project also helped strengthen my understanding of AWS services, Linux system administration, Docker containerization, and Jenkins pipeline automation — all essential skills for a career in DevOps and Cloud Engineering.

In this project, I deployed Jenkins on AWS using Terraform, Docker, and Ubuntu Linux. I provisioned an EC2 instance via Terraform, installed and configured Jenkins and Ansible, and containerized applications with Docker. Key steps included transferring configuration files, setting up AWS CLI credentials, handling SSH access, and troubleshooting issues like incorrect file permissions and key path errors. After setting up Jenkins, I created and ran a freestyle job to execute shell commands remotely. Finally, I destroyed all AWS resources using Terraform to avoid additional charges. This project highlights my skills in Infrastructure as Code (IaC), CI/CD pipelines, AWS cloud management, and Linux administration.

## Setup Overview

• Terraform: For infrastructure provisioning.

• Docker/Docker Compose: For application containerization.

• AWS CLI: For interaction with AWS services.

• VSCode: Code Editor

• Ubuntu Linux environment

• AWS account with access keys

## Step-by-Step Implementation

1. Transferred Terraform and Docker files to the Ubuntu instance via scp.

2. Installed Terraform by adding HashiCorp repositories and verifying the setup.

3. Configured AWS CLI by setting up IAM credentials and validating with aws sts get-caller-identity.

4. Provisioned AWS Infrastructure:

    o Ran terraform init, terraform fmt, terraform validate, terraform plan, and terraform apply.

    o Corrected Windows path format issues for SSH keys in variables.tf.

5. Connected to the EC2 instance securely using SSH.

6. Installed and Configured Jenkins:

    o Retrieved the initial admin password.
    o Installed suggested plugins.
    o Created an admin user and configured the first Jenkins job.

7. Installed Ansible on the EC2 instance to prepare for configuration management automation.

8. Built the first Jenkins job that executed shell commands on the EC2 instance.

9. Tore down the infrastructure after the lab using terraform destroy to avoid incurring unnecessary AWS charges.

## Troubleshooting and errors which I faced during this project implementation

**1. AWS CLI Configuration Problems:**

If aws configure fails, verify that your AWS Access Key and Secret Key are correct.

Run aws configure and input your credentials

Confirm setup by running the following command

aws sts get-caller-identity

**2. Permission Denied During SSH:**

Ensure you're using the correct private key and the correct username (ec2-user).And ensure you're using the correct private key with the -i option.

Ex: ssh -i /path/to/your-key.pem ec2-user@98.80.171.157

ssh -i /root/.ssh/docker ec2-user@98.80.171.157

**3. Terraform Plan Errors:**

Check if the paths to private/public keys are correctly defined in the variables.tf file (under modules -compute - variables.tf)

Issue: Invalid SSH key path error like "C:\\Users\\wessa\\.ssh\\id_rsa.pub"

Fix: Update variables.tf to point to a valid Unix-style path, e.g. /root/.ssh/docker.pub

**4. Access denied to files:**

After transferring the files from windows to linux you need to give following permission so that we can save and edit files

sudo chown -R administrator:administrator /home/administrator/Terraform-Docker

sudo chmod -R u+rwX /home/administrator/Terraform-Docker

## Step1: Transfer Terraform and yml files

Use scp to transfer your Terraform and docker files from your local machine to your Ubuntu instance:

Note: Transfer the terraform and docker code from local machine to Linux by running following command on cmd

scp -r -v " C:\Users\Gurpreet\OneDrive\Desktop\York Univ\Assignments\Assignment-5-Terraform-Jenkins\Terraform-Jenkins" administrator@10.0.0.83:/home/administrator

Enter your password of ubuntu user

It shows the following screen after transfer

![Image1](https://github.com/gurpreet2828/Terraform-Jenkins/blob/d3c64f054608028ade0e1933e4951a68f690774a/Images/Image1.png)

Step2: Install Terraform and AWS on Ubuntu
1. Update and install dependencies
sudo apt update && sudo apt install -y gnupg software-properties-common curl
2. Add the HashiCorp GPG key
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
3. Add the HashiCorp repo
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list
4. Update and install Terraform
sudo apt update
sudo apt install terraform -y
5. Verify installation
terraform -v
 

AWSCLI Install
To install the AWS CLI, run the following command
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
Run the following command to check if AWS CLI is installed correctly:
aws –version
You see the following output
 

Step 3: Create AWS account 
After Creating 
Click on account name - Select Security Credentials
 








Click Create access key.
 

Note: Download the key file or copy the Access Key ID & Secret Access Key (Secret Key is shown only once!).
After install and creating AWS account configure the AWS 
Configure AWS CLI with the New Access Key
aws configure
It will prompt you for:
1.	AWS Access Key ID: Your access key from AWS IAM.
2.	AWS Secret Access Key: Your secret key from AWS IAM.
3.	Default region name: (e.g., us-east-1, us-west-2).
4.	Default output format: (json, table, text — default is json).
Enter access key and secret key which you will get from aws account
Check credentials added to aws configure correctly
aws sts get-caller-identity
If your AWS CLI is properly configured, you'll see a response like this:

 
Provisioning Infrastructure with Terraform
Step1: Terraform init
 
Step 2: Terraform fmt
Step 3: Terraform validate
 
Step 4: Terraform plan
 
Run Terraform plan 
If it shows the following error
Error: Invalid function argument
│ 
│   on modules/compute/main.tf line 17, in resource "aws_key_pair" "aws-key":
│   17:   public_key = file(var.ssh_key_public)
│     ├────────────────
│     │ while calling file(path)
│     │ var.ssh_key_public is "C:\\Users\\wessa\\.ssh\\id_rsa.pub"
 
Then in this case you must update the location of public and private keys under modules -compute - variables.tf

















As shown bellow in image

 

5: Terraform apply
Provision terraform managed infrastructure. You must confirm by trying yes if you would like to continue and perform the actions described to provision your infrastructure resources

 
If everything works fine at end you will see the public Ip

Step 4: Connect to EC2 instance 
Ensure you're using the correct private key and the correct username (ec2-user) and ensure you're using the correct private key with the -i option.
Example
ssh -i /path/to/your-key.pem ec2-user@ 18.204.55.71
ssh -i /root/.ssh/docker ec2-user@ 18.204.55.71
you will see ec2 instance connected remotely with your ubuntu instance
 

You will see Jenkins and Ansible install on ec2 instance
Check by running the following commands
Jenkins –version
Ansible --version
 
Step 5: Connect to Jenkins
•	Now you can access Jenkins using the URL provided in the output
•	See the password by running the following command
cat /var/lib/jenkins/secrets/initialAdminPassword

 

Enter the displayed password 

 

i.	Double click on install suggested plugins














ii.	Create your admin user by completing all the requested information

 

iii.	Then Click save and continue you find the following screen

 


Then click Save and Finish

 

Click on Start Jenkins

You will see the following screen

 


iv.	Build first job

Click on create job 

 

Enter the name of the job and click on Freestyle Project

 

Scroll down to Build

Select Add build step -> Execute shell

 

Enter the following commands to run these commands on ec2 instance
 
Click Save 

After this you will see the following screen – click build Now

 

Notice under the Build History, you will find build now. Click on the small dop down arrow near #1 and go to Console Output

 

Notice under the Build History, you will find build now. Click on the small dop down arrow near #1 and go to Console Output

 

Terraform destroy
o	Once you are done with the lab you must destroy the provisioned infrastructure resources to avoid any risks of encountering cost in the future.
o	terraform destroy: Destroy terraform managed infrastructure. You must confirm by trying yes

 
