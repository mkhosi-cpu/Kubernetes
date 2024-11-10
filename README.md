# Kops for a Cluster on AWS

# Setup with Kops

# Prerequisites

- Domain for Kubernetes DNS records
- Create a linux VM and setup 
  - kops , kubectl , ssh keys , awscli
- Login to AWS account and setup 
  - s3 bucket ,IAM user for awscli , Route53 Hosted Zone



# Log onto AWS ec2 instance using git bash

ssh -i /path/key-pair-name.pem instance-user-name@instance-public-dns-name

# Configure ec2 instance

Install awscli 
sudo apt update && sudo apt install awscli -y

aws configure
- Enter your AWS Access Key ID
- Enter your AWS Secret Access Key
- Enter default region (e.g., us-west-2)
- Enter output format (json)

# Install Kubectl

  curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

  chmod +x kubectl

  sudo mv kubectl /usr/local/bin/

#  Install Kops

wget https://github.com/kubernetes/kops/releases/download/v1.26.4/kops-linux-amd64

chmod +x kops-linux-amd64

sudo mv kops-linux-amd64 /usr/local/bin/kops

# Create cluster using kops

The below command will store the configuration on a S3 bucket

 kops create cluster --name=kubevpro.dev-keleza.site --state=s3://vprofile-kops-sate --zones=us-east-1a,us-east-1b --node-count=2 --node-size=t3.small --master-size=t3.medium --dns-zone=kubevpro.dev-keleza.site --node-volume-size=8 --master-volume-size=8

 - Provision the cluster with the below command

 kops update cluster --name kubevpro.dev-keleza.site --state=s3://vprofile-kops-sate --yes --admin

 kops validate cluster --state=s3://vprofile-kops-sate

 kops delete cluster --name=kubevpro.dev-keleza.site --state=s3://vprofile-kops-sate --yes  - to delete the cluster

