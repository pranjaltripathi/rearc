Rearc Deployment Project
This repository contains the deployment of a Dockerized Node.js application on AWS infrastructure, including EC2, ECR, ALB, and ACM for HTTPS. The project showcases the use of Infrastructure as Code (IaC) with AWS CloudFormation, Docker, and Load Balancer configuration for a scalable and secure deployment.

Features
Dockerized Node.js application for easy portability.
AWS Elastic Container Registry (ECR) for managing Docker images.
EC2 instance to host the application.
Application Load Balancer (ALB) for traffic routing and scalability.
Health checks and secure HTTP access.
Prerequisites
AWS CLI installed and configured with appropriate IAM permissions.
Docker installed locally.
Access to an AWS account.
Deployment Steps
Step 1: Clone the Repository
Clone this repository to your local machine:

bash
Copy code
git clone https://github.com/pranjaltripathi/rearc.git
branch :- Master
cd rearc
Step 2: Build and Push Docker Image to ECR
Authenticate Docker with Amazon ECR:

bash
Copy code
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 017820661592.dkr.ecr.us-east-1.amazonaws.com
Build the Docker image:

bash
Copy code
docker build -t webapp .
Tag the Docker image:

bash
Copy code
docker tag webapp:latest 017820661592.dkr.ecr.us-east-1.amazonaws.com/webapp:latest
Push the Docker image to ECR:

bash
Copy code
docker push 017820661592.dkr.ecr.us-east-1.amazonaws.com/webapp:latest
Step 3: Deploy Infrastructure with CloudFormation
Use the provided CloudFormation template (cft.yaml) to deploy the AWS resources.

Validate the CloudFormation template:

bash
Copy code
aws cloudformation validate-template --template-body file://cft.yaml
Create a CloudFormation stack:

bash
Copy code
aws cloudformation create-stack --stack-name webapp-stack --template-body file://cft.yaml --capabilities CAPABILITY_NAMED_IAM
Wait for the stack creation to complete:

bash
Copy code
aws cloudformation wait stack-create-complete --stack-name webapp-stack
Step 4: Configure the EC2 Instance
SSH into the EC2 instance:

bash
Copy code
ssh -i <key-pair.pem> ec2-user@<EC2_PUBLIC_IP>
Install Docker and start the service:

bash
Copy code
sudo yum update -y
sudo yum install -y docker
sudo service docker start
Run the Docker container on EC2:

bash
Copy code
docker run -d -p 3000:3000 017820661592.dkr.ecr.us-east-1.amazonaws.com/webapp:latest
Step 5: Configure Application Load Balancer
Set up an Application Load Balancer (ALB) with the following configuration:

HTTP Listener (Port 80): Forward to the target group with EC2 instance on port 3000.
HTTPS Listener (Port 443): Use the certificate from ACM for secure access.
Target Group:

Health check path: /
Protocol: HTTP
Port: 3000


Use the ALB DNS name or your custom domain name.
Validate the domain via DNS (CNAME record).
Attach the certificate to the ALB:



Step 7: Test the Deployment
Access the application via the Load Balancer's DNS:
bash
Copy code
http://<ALB_DNS>:3000
Securely access the application with HTTPS:
bash
Copy code
https://<ALB_DNS>
Directory Structure
graphql
Copy code
rearc/
│
├── Dockerfile              # Dockerfile to build the application image
├── cft.yaml                # CloudFormation template for AWS resources
├── 000.js                  # Node.js application file
├── package.json            # Application dependencies
├── README.md               # Project documentation
└── screenshots/            # Screenshots of deployment
Improvements and Next Steps
Given more time, I would:

Implement auto-scaling for the EC2 instances behind the ALB to handle increased traffic.
Set up a CI/CD pipeline with AWS CodePipeline or Jenkins for automated builds and deployments.
Add application monitoring and logging using CloudWatch or a third-party tool like Datadog.
Configure a custom domain using Route 53 for better branding.
Proof of Deployment
Live URL:
arduino
Copy code
http://webapp-loadb-qlqqi6whnmua-32587256.us-east-1.elb.amazonaws.com:3000/
Screenshot: See screenshots/deployment.png.
