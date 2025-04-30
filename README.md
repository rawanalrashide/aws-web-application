AWS Web Application Deployment with ALB + ASG + S3

This project demonstrates the setup of a static website using S3 and a dynamic infrastructure using EC2 Auto Scaling Group behind an Application Load Balancer (ALB). The deployment includes a basic HTML file and images hosted on S3, and the same website is dynamically served by EC2 instances through Nginx.

Part 1: Static Website Hosting on S3

	•	S3 Bucket Name: rawan-clarusway-assets
	•	Static Website Hosting URL:
http://rawan-clarusway-assets.s3-website.eu-north-1.amazonaws.com/
	•	Uploaded files:
	•	index.html
	•	logo.png
	•	sda.png
	•	Public access enabled through a bucket policy

Part 2: EC2 Launch Template and IAM Role

	•	IAM Role: EC2S3ReadOnlyRole
	•	Attached policy: AmazonS3ReadOnlyAccess
	•	Launch Template Name: clarusway-launch-template
	•	AMI: Amazon Linux 2
	•	Instance Type: t3.micro
	•	IAM Role: EC2S3ReadOnlyRole
	•	User Data:

#!/bin/bash
yum update -y
yum install nginx -y
systemctl start nginx
systemctl enable nginx
aws s3 cp s3://rawan-clarusway-assets/index.html /usr/share/nginx/html/index.html
aws s3 cp s3://rawan-clarusway-assets/logo.png /usr/share/nginx/html/logo.png
aws s3 cp s3://rawan-clarusway-assets/sda.png /usr/share/nginx/html/sda.png



Part 3: Auto Scaling Group and Load Balancer

	•	Auto Scaling Group Name: clarusway-asg
	•	Launch template: clarusway-launch-template
	•	Capacity: Min=1, Desired=2, Max=3
	•	Application Load Balancer Name: clarusway-alb
	•	DNS Name:
http://clarusway-alb-259984749.eu-north-1.elb.amazonaws.com
	•	Target Group Name: clarusway-tg
	•	Type: Instance
	•	Health check path: /

Testing

	•	Verified the Load Balancer serves content correctly:
	•	http://clarusway-alb-259984749.eu-north-1.elb.amazonaws.com
	•	PowerShell command used for testing:

for ($i=1; $i -le 5; $i++) { Invoke-WebRequest -Uri "http://clarusway-alb-259984749.eu-north-1.elb.amazonaws.com" -UseBasicParsing }



Notes

	•	S3 bucket permissions set for public read access
	•	EC2 instances configured with IAM role for S3 access
	•	Security group rules allowed HTTP access on port 80 from anywhere
	•	Images successfully loaded from S3 to Nginx on EC2
