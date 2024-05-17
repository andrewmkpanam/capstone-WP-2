## This ReadME file contains the documentation for the capstone project WordPRess Site on AWS

## Networking:

I setup 6 different subnets as follows:

1. 2 public subnets split between 2 availability zones 
        public-subnet-AZ1 - 10.0.0.0/24 
        public-subnet-AZ2 - 10.0.1.0/24
2. 2 App subnets split between 2 availability zones

        private-app-subnet-AZ1 - 10.0.2.0/24
        private-app-subnet-AZ2 - 10.0.3.0/24

3. 2 Data subnets split between 2 availability zones.
        private-data-subnet-AZ1 - 10.0.4.0/24
        private-data-subnet-AZ2 - 10.0.5.0/24

2 NAT gate ways were provisioned in the public subnet and an internet gate way was provisioned for the VPC. 

Based on the diagram provided route table were configured with the private subnets pointing to the NAT gateways and the public subnets pointing to the internet gateway.

# Security Groups

ALB Security Group : Port 80 and 443 Source: 0.0.0.0/0
SSH Security Gorup : port 22 Source : SSH IP

Web Server Security Group

Port 80 and 443 Source : ALB security group

RDS Security Group : Port 3306 Source : Webserver Security Group

EFS Security Group Port: 2049 and 22 Source : Webserver Security Group, SSH Security Group

## Services Provisioned

1. RDS instance
2. EFS file system with mount targets in 2 availability zones
3. Prototype webserver - Amazon linux Image : WP installed on this and an image created  for launch consiguration
4. Appllication Load Balancer
5. Target Group for Autoscaling
6. Auto scaling configuration


Install Commands / User Data

'''
sudo yum update -y

sudo yum install php -y
sudo yum install php-mysqli -y
sudo yum install mariadb105 -y

sudo systemctl start httpd
sudo systemctl enable httpd

sudo systemctl start php-fpm
sudo systemctl enable php-fpm

EFS mount

sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-020a682c5ba2f0fc3.efs.us-west-1.amazonaws.com:/ /var/www/html/

wget https://wordpress.org/latest.zip
unzip latest.zip
sudo cp -R wordpress/* /var/www/html/

COnnect to RDS

mysql -h wp-db.cb7yjv0w5dmw.us-west-1.rds.amazonaws.com -P 3306 -u admin -p

Create DB

create databases project;

Although I did not use this in the user data because I created an image after the install to use in the launch configuration for the Auto scaling group.

ALB A record: http://alb-wb-1750283094.eu-north-1.elb.amazonaws.com

