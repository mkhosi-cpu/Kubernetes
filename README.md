# Kubernetes
EKS cluster with test and production environments to host an application.
All the below steps to be executed inside an EC2 instance

Launch an EC2 instance and begin the prerequisites

# Prerequisites Installation on a EC2 instance

# Install AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Install Terraform
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform

# Configure AWS Credentials

aws configure
- Enter your AWS Access Key ID
- Enter your AWS Secret Access Key
- Enter default region (e.g., us-west-2)
- Enter output format (json)

# Create EKS Cluster using Terraform

# Initialize Terraform
terraform init

# Review the plan
terraform plan

# Apply the configuration
terraform apply

# Configure kubectl for EKS
aws eks update-kubeconfig --region us-east-1 --name KelezaIT

# Deploy the Applications

# Create namespaces
kubectl apply -f namespaces.yaml

# Deploy test environment
kubectl apply -f deployment-test.yaml
kubectl apply -f service-test.yaml

# Deploy production environment
kubectl apply -f deployment-prod.yaml
kubectl apply -f service-prod.yaml

# Verify Deployments

# Check test namespace
kubectl get pods -n test
kubectl get services -n test

# Check production namespace
kubectl get pods -n production
kubectl get services -n production

# Access the Applications

# Get the Load Balancer URLs
TEST_URL=$(kubectl get svc sample-app-service -n test -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')
PROD_URL=$(kubectl get svc sample-app-service -n production -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')

# Test the applications
curl http://$TEST_URL
curl http://$PROD_URL

Key differences between test and production environments:

Test environment runs 1 replica, production runs 3
Production has higher resource limits
Both environments use AWS Load Balancers for access

# To clean up and avoid unnecessary costs:

# Delete Kubernetes resources
kubectl delete namespace test
kubectl delete namespace production

# Destroy EKS cluster and associated resources
terraform destroy

# Important notes:

The sample configuration uses t3.medium instances. Adjust based on your needs
The VPC is created with public and private subnets in two availability zones
Replace the nginx image with your application image
Consider adding:

AWS Load Balancer Controller for better integration
External DNS for automatic DNS management
Cluster Autoscaler for automatic scaling
AWS Certificate Manager for SSL/TLS

