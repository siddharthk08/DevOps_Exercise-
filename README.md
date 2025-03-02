# DevOps_Exercise-
This project deploys an AWS EC2 instance running a Flask application behind an Application Load Balancer (ALB) using Terraform. The deployment includes a custom security group, VPC configuration, and Gunicorn & Nginx for production readiness.

#Procedure
--**Configuring Security Group for Port 5000**-
By default, the security group allowed only port 80 (HTTP).
    Since Flask runs on port 5000, I added an ingress rule to allow traffic on port 5000 from all sources (0.0.0.0/0).
    This was necessary for both load balancer target group health checks and direct testing.

--**Running the Terraform Script ** -
First, I executed the Terraform script to create an EC2 instance and ALB setup.
    Encountered an issue with the AWS region, as it needed to be changed to my desired region (ap-south-1).
    Updated the AMI ID to match my region's available Amazon Linux 2 AMI.
    
--**Defining VPC Manually Instead of Using a Dynamic One**-
By default, Terraform can dynamically fetch the VPC ID but sometimes it doesn't extracts vpc when newly account created.
    To avoid misconfiguration, I manually defined the VPC ID instead of relying on data.aws_vpc.default.id.
    This ensured that all subnets and resources were correctly assigned within the intended network.

--**Deploying Flask App on EC2 & Running It with Gunicorn and Nginx**-
Running Flask with python app.py worked only locally and did not serve traffic over the           public ALB DNS.To enable production-level hosting, I used Gunicorn as a WSGI server to handle requests outside the localhost.This setup allowed the Flask app       to serve requests externally through the ALB.
    
--**Security Group Not Connecting to Load Balancer**-
During troubleshooting, I found that the ALB target group was correctly defined in Terraform, and the EC2 instance was properly registered with the target group. However, the security group for the ALB was missing — it was not defined in Terraform.
Fix: I manually attached the correct security group to the ALB via the AWS Console, since it was missing from the Terraform configuration.

--**Application is Healthy & Running**-
After attaching the instance correctly and using Gunicorn + Nginx, the ALB health check marked the target group as healthy.
    The application was accessible via ALB DNS, confirming successful deployment.

--**Additional tasks**-
Running Terraform Locally Instead of EC2(Running it locally provides better control, easier debugging, and local state management.)
    Performing Health Checks Using AWS CLI Instead of Web Console(This provides faster and more scriptable health checks instead of relying on the AWS website.)
    Ran it firstly by localhost to check its working 
    Also,one already instance was created even added that to load balancer registery nad even hosted flask server in it to try out does load on Ec2 instance shifts     to another if one Ec2 instance gets to much of requests
  
