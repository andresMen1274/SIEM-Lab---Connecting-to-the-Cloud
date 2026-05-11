# SIEM-Lab---Connecting-to-the-Cloud
I will be connecting my home lab to the cloud and allow for remote access to resources. To start this lab create a free AWS account. Then we will configure a VPC(virtual private cloud) search VPC -> create VPC. Select VPC and more, name the VPC(in my case it is named SIEM-lab-VPC), set number of avaliability zones to 1, number of public subnets to 1, number of private subnets 0, and no NAT gateways. Now after the VPC has been created we will launch our first EC2 instance. Navigate to EC2 -> instance -> launch instances and name the EC2 instance. The OS image I have selected is the AWS 2023 Linux image, t3.micro for instance type, create a new key pair(leave it as RSA encryption), set the VPC to the newly created SIEM-lab-VPC, enable auto assign public IP, allow SSH and only allow connections from My IP, Create a new security group(name it), and launch instance. 

Now we will make sure that the VPC and instance has been created. To do this naviagte to VPC -> your VPCs. To check that the instance has been successfully created by navigating to EC2 -> instances. Important note make sure that the EC2 instance is stopped while it is not in use. 

<img width="1596" height="292" alt="image" src="https://github.com/user-attachments/assets/75688fb7-b3df-4246-966f-53e9b7146130" />

<img width="1602" height="231" alt="image" src="https://github.com/user-attachments/assets/38374ced-ed80-4747-b30d-061c36e04ab6" />

Now I will connect to the instance that I have created. Now using the key that was created we will ssh into the instance. This is done by entering this command.

ssh -i .\<key-name>.pem ec2-user@<> <-- IP address

<img width="841" height="252" alt="image" src="https://github.com/user-attachments/assets/69bd2c72-b04f-4f1b-bb7a-7ebd847f1181" />

