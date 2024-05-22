EC2 Memory and Disk Monitoring
Monitor EC2 memory and disk utilization using CloudWatch. This guide demonstrates how to set up AWS EC2 monitoring with CloudWatch, focusing on memory and disk utilization.

Steps
Step 1: Create an AWS EC2 Instance and Attach an IAM Role
Ensure the IAM role has permissions for CloudWatch and SSM Full Access.

Step 2: Create a Parameter in Systems Manager
Name the parameter "/alarm/AWS-CWAgentLinConfig" and store the configuration value provided below.

Step 3: Apply the Script on the Server
Add the following commands to the User Data section when launching your EC2 instance:

bash
Copy code
#!/bin/bash
sudo apt update -y
sudo apt install wget unzip zip -y
wget https://s3.amazonaws.com/amazoncloudwatch-agent/linux/amd64/latest/AmazonCloudWatchAgent.zip
unzip AmazonCloudWatchAgent.zip
sudo ./install.sh
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c ssm:/alarm/AWS-CWAgentLinConfig -s
Verify the CloudWatch Agent installation:

bash
Copy code
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -m ec2 -a status
Value for the SSM Parameter (/alarm/AWS-CWAgentLinConfig):
json

{
	"metrics": {
		"append_dimensions": {
			"InstanceId": "${aws:InstanceId}"
		},
		"metrics_collected": {
			"mem": {
				"measurement": [
					"mem_used_percent"
				],
				"metrics_collection_interval": 60
			},
			"disk": {
				"measurement": [
					"disk_used_percent",
					"used_percent"
				],
				"metrics_collection_interval": 60,
				"resources": [
					"/"
				]
			}
		}
	}
}

This configuration enables CloudWatch to collect memory and disk usage metrics from your EC2 instance every 60 seconds.







