# Cloudwatch

EC2 Memory Disk Monitoring
Monitor EC2 Memory and Disk Utilisation using CloudWatch | AWS EC2 Monitoring with Cloudwatch | Monitor Memory and Disk Utilisation using CloudWatch | AWS CloudWatch Demo

Steps:
Step 1: Create an AWS EC2 & Attach IAM Role which have permissions of CloudWatch and SSM Full Access.
Step 2: Create a Parameter in Systems Manger with the name "/alarm/AWS-CWAgentLinConfig" and store the value which is Below.
Step 3: Apply Script on Server

Commands that needs to be added in Userdata Section:
#!/bin/bash
sudo apt update -y
sudo apt install wget unzip zip -y
wget https://s3.amazonaws.com/amazoncloudwatch-agent/linux/amd64/latest/AmazonCloudWatchAgent.zip
unzip AmazonCloudWatchAgent.zip
sudo ./install.sh
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c ssm:/alarm/AWS-CWAgentLinConfig -s
Check if EC2 Instance has CWAgent Installed or not:
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -m ec2 -a status


**Value for the SSM Parameter (/alarm/AWS-CWAgentLinConfig):**

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
                     "disk_used_percent"
				],
				"metrics_collection_interval": 60
			}
		}
	}
}
