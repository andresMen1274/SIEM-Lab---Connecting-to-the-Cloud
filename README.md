# SIEM-Lab---Connecting-to-the-Cloud
I will be connecting my home lab to the cloud and allow for remote access to resources. To start this lab create a free AWS account. Then we will configure a VPC(virtual private cloud) search VPC -> create VPC. Select VPC and more, name the VPC(in my case it is named SIEM-lab-VPC), set number of avaliability zones to 1, number of public subnets to 1, number of private subnets 0, and no NAT gateways. Now after the VPC has been created we will launch our first EC2 instance. Navigate to EC2 -> instance -> launch instances and name the EC2 instance. The OS image I have selected is the AWS 2023 Linux image, t3.micro for instance type, create a new key pair(leave it as RSA encryption), set the VPC to the newly created SIEM-lab-VPC, enable auto assign public IP, allow SSH and only allow connections from My IP, Create a new security group(name it), and launch instance. 

Now we will make sure that the VPC and instance has been created. To do this naviagte to VPC -> your VPCs. To check that the instance has been successfully created by navigating to EC2 -> instances. Important note make sure that the EC2 instance is stopped while it is not in use. 

<img width="1596" height="292" alt="image" src="https://github.com/user-attachments/assets/75688fb7-b3df-4246-966f-53e9b7146130" />

<img width="1602" height="231" alt="image" src="https://github.com/user-attachments/assets/38374ced-ed80-4747-b30d-061c36e04ab6" />

Now I will connect to the instance that I have created. Now using the key that was created we will ssh into the instance. This is done by entering this command.

ssh -i .\<key-name>.pem ec2-user@<> <-- IP address

<img width="841" height="252" alt="image" src="https://github.com/user-attachments/assets/69bd2c72-b04f-4f1b-bb7a-7ebd847f1181" />

Now I will check that everything has been configured correctly by entering these commands to see user, host name, and ip address.

whoami
hostname
ip addr

<img width="1140" height="487" alt="image" src="https://github.com/user-attachments/assets/946a576c-c92b-4401-bfe9-e157500dd251" />

Now to make sure that the instance is updated and configure common tools are configured, enter these commands as follows:
sudo dnf update -y
sudo dnf install -y nmap tcpdump git htop

Now we will generate some logs to make sure that we can view them. This can be done in various ways, but I will do so by running the commands:
curl ifconfig.me
ping -c 4 google.com
sudo nmap -sS localhost

CloudTrail is a service that is provided by AWS which used to record, monitor, and retain account information across AWS infastructre. Since we want to gather logs from each user we will create a trail. To do this navigate to CloudTrail -> create trail -> name the trail and select create trail. 

<img width="1887" height="348" alt="image" src="https://github.com/user-attachments/assets/5803da81-fa99-4bb4-b92c-3377d58c9bb2" />

Now stop the instance and start it again. Then navigate to CloudTrail -> event history and view the logs that show the stopping and starting of the instance. Now that we have a good view on account information we want to monitor Network traffic. To do this we navigate to VPC -> select the VPC that you created -> open flow logs tab -> select create flow log. Name the flow logs, set to collect all logs, 10 minute agregation, send to CloudWatch logs, create and use a new service role, and AWS defalut format. 

<img width="1366" height="647" alt="image" src="https://github.com/user-attachments/assets/9ca1d5b3-e8c3-40c3-8599-07270ea756a8" />

To make sure that logs are working naviagte to CloudWatch -> logs -> log management -> select your group. Then view the logs that have been generated. 

<img width="1567" height="258" alt="image" src="https://github.com/user-attachments/assets/8e6b67ca-a185-45e2-92fa-15c10fa8f560" />

Now that we have confirmed that the logs are being forwarded correctly we will connect this to our home lab. To do this start the wazuh server and ssh into it using the command ssh wazuh@10.200.20.10. After that has been completed then enter the commands:

sudo apt update
sudo apt install awscli -y

What these commands do is update the Wazuh server and add the aws extension to forward logs to the Wazuh server. Now we will create a IAM user that will be used for AWS logs. naviagte to IAM -> IAM users -> create user. Then name the user, select attach policies directly(attach the policy AmazonS3ReadOnlyAccess), and then create user. After it has been correctly created then select security credentials and create access key. Select Command Line Interface(CLI) then select next -> create access key. Make sure that when the Access and secret keys are displayed that you save them becuase they will not be shown again. After this step has been done go back to the wazuh server and enter aws configure. Enter in the acces and secret keys. Input the region. modify the ossec.conf file with this command:

sudo nano /var/ossec/etc/ossec.conf

Add this block at the bottom of the configuration file.

<wodle name="aws-s3">
  <disabled>no</disabled>
  <interval>10m</interval>
  <run_on_start>yes</run_on_start>

  <bucket type="cloudtrail">
    <name>aws-cloudtrail-logs-190986995757-062883f2</name>
    <aws_profile>default</aws_profile>
  </bucket>
</wodle>

These commands update the permissions so that it is able to access the profile. Then viewing the logs for any configuration errors.
sudo cp -r ~/.aws /root/
sudo chown -R root:root /root/.aws
sudo systemctl restart wazuh-manager
sudo tail -f /var/ossec/logs/ossec.log
