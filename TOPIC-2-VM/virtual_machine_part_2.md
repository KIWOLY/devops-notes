how to create virtual machine  in AWS 
         on aws console yo will  write the request of creating the VM (EC2)
         AWS will create using hypervisor and return to you with the public ip 



what if you have to create the 100 VM in AWS 
    you will do it manually in 100 times ? 

    note when you create the VM in AWS example EC2   you send the request of create usig 
    EC2 API



how devops come in 
    is to improve delivery and  effecienty 

for the above case you will look for automation 
     where by   operator he will write the scripts(for creating instance of 100 VPS )  and calling AWS EC2 API 
     requesting for 100 instance using  AWS EC2 API  and   AWS will respond 100 ES2 instance (but all will be done when all request is valid , user who sending the request must be aunthenticated and authorized to send that request)


what is Script  
    can be in multiple types one is through CLI
     example in AWS     is AWS CLI 

    
    another is AWS API

    another is AWS    CFT

    Terraform .........used on multiple cloud platform


how to create  aws instance 

To create an AWS instance (a virtual server in the cloud), follow these steps:

1. Go to AWS Management Console and sign in.
2. Open Amazon EC2.
3. Click Launch Instance.
4. Enter a name for your instance (e.g., MyServer).
5. Choose an operating system image (AMI), such as:
     Ubuntu Server
     Amazon Linux
6. Select an instance type (e.g., t2.micro or t3.micro, which are often eligible for the AWS Free Tier).
7. Create or select a key pair (.pem file) for SSH access and download it safely.
8. Configure network settings:
     Allow SSH (port 22) for Linux.
     Allow HTTP (port 80) and HTTPS (port 443) if hosting a website.
9. Configure storage (the default size is usually sufficient for testing).
10. Click Launch Instance.




Steps to Connect to an AWS EC2 Instance Using SSH
Open a terminal on Ubuntu.

Go to the folder containing your .pem key file:

cd ~/Downloads/aws_key

Set the correct permissions on the key file:

chmod 400 key_aws_air.pem

Connect using the instance's Public IP Address:

ssh -i key_aws_air.pem ubuntu@PUBLIC_IP

Example:

ssh -i key_aws_air.pem ubuntu@54.123.45.67

Accept the fingerprint the first time you connect:

Are you sure you want to continue connecting (yes/no)?

Type:

yes


chmod 400 sets the file permissions so that only you (the owner) can read it — nothing else.

Breaking it down:
chmod takes a 3-digit number, each digit representing permissions for:
DigitWhoFirst 4Owner (you)Second 0GroupThird 0Others
Each digit is a sum of:

4 = read
2 = write
1 = execute
0 = no permission

So 400 = owner can read only, group and others have zero access.