# Launching an EC2 instance using AWS CLI

AWS CLI is a tool for running and managing your various AWS services. Just download and install the tool.

https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

Creating VPC 
--------------
Create a VPC under which an EC2 instance will be launched. For creating a VPC in CLI type the given command on the cmd.

        aws ec2 create-vpc --cidr-block 10.0.0.0/16
        
Creating Subnets
-----------------

Create two subnets and make one as public to make it accessible from the internet.

      aws ec2 create-subnet --vpc-id <vpcId> --cidr-block 10.0.1.0/24
      
Create a second subnet with CIDR block 10.0.0.0/24 (CIDR block values can be changed as per user needs)

     aws ec2 create-subnet --vpc-id <vpcId> --cidr-block 10.0.0.0/24

 Creating Internet Gateway
------------------------

Internet gateway is used by the private subnet to access the internet for its updates and other packages installations. Create an internet gateway.

    aws ec2 create-internet-gateway

After the internet gateway is created, note the InternetGatewayId and to attach this internet gateway to the already created VPC

    aws ec2 attach-internet-gateway --vpc-id <vpcId> --internet-gateway-id <InternetGatewayId>

Creating Route Table
---------------
 
The next step is to create a route table and assigning it to the already created VPC. After creating the route table assign the route to this route table.

    aws ec2 create-route-table --vpc-id <vpcId>

RouteTableId and use it in the next step:

    aws ec2 create-route --route-table-id <RouteTableId> --destination-cidr-block 0.0.0.0/0 --gateway-id <nternetGatewayI>
    

Viewing the Route Table and Subnets
-------------------------------
To check whether route table and subnets are created and assigned successfully use the below commands.

    aws ec2 describe-route-tables --route-table-id <RouteTableId>
    
    
    aws ec2 describe-subnets --filters "Name=vpc-id,Values=<vpcId>" --query "Subnets[*].{ID:SubnetId,CIDR:CidrBlock}"

Associating Route Table and modifying subnet
------------------------------------------
    
The next step is to associate the route table with the subnet and making the same subnet as public by mapping the public IP address to it. Enter the SubnetId and RouteTableId that you noted earlier.  To associate route table type.

    aws ec2 associate-route-table --subnet-id <SubnetId> --route-table-id <RouteTableId>
    
To map the public IP to the subnet, use the below command:

    aws ec2 modify-subnet-attribute --subnet-id <SubnetId> --map-public-ip-on-launch

Creating Key Pair and Security Group
--------------------------------
The most important step is to create a key pair. This key pair must be kept safe and secure with the user so that the person can access the EC2 instance created using this key pair.

Now, 
Create the key-pair using the below command:

    aws ec2 create-key-pair --key-name AWS-Keypair --query "KeyMaterial" --output text > "C:\AWS\AWS_Keypair.pem"
    
For security group use the below commands:

    aws ec2 create-security-group --group-name <security-group-name> --description "<description>" --vpc-id <vpcId>
    

    aws ec2 authorize-security-group-ingress --group-id <GroupId> --protocol tcp --port 22 --cidr 0.0.0.0/0
    
The protocol/port we use here is TCP/22.

Running the EC2 Instance
-------------------------

After all the setup completed successfully now the time is to run the instance. For running the EC2 Instance use the command as given below.

    aws ec2 run-instances --image-id <ami-id> --count 1 --instance-type t2.micro --key-name <Keypair-name> --security-group-ids <SecurityGroupId --subnet-id <SubnetId>

Viewing the Instance
------------------------------
Now after the instance status is “running” type the command to view the complete details of the EC2 instance that you just created:

    aws ec2 describe-instances --instance-id <InstanceId>



