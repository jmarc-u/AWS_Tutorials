Course: AWS Solution Architect Associate
Assignment: Install & Run R Server + Documentation (+understanding AWS)
Due date: 24/11/2017

Author: Jean-Marc Uzé
Version: 1.1

Exec Summary:
-	we launch a standard EC2 instance
-	we install R, R Server, and Shiny Server (the latest not requested but it looks cool !) with a combination of user data and SSH commands
-	we check and test
-	we save the image as a personal AMI for future use without reinstallation (in order to gain time when launching future instances




Step 1) Select AMI : Amazon Linux AMI 2017.09.1 (HVM), SSD Volume Type

Step 2) Select Instance : t2.micro because it is free, although t2. Medium is recommended by Amazon for RServer.

Step 3) select default network and subnet

Step 4) activate automatic public address assignment

Step 5) Add following user data code according instructions on 
<https://aws.amazon.com/fr/blogs/big-data/running-r-on-aws/>

Take care of loading the latest version of RStudio checking on <https://www.rstudio.com/products/rstudio/download-server/>

Note : it must be a RedHat/CentOS version, 64 bits
```
#!/bin/bash
#install R
yum install -y R

#install RStudio-Server 1.0.153 (2017-11-22)

wget https://download2.rstudio.org/rstudio-server-rhel-1.1.383-x86_64.rpm

yum install -y --nogpgcheck rstudio-server-rhel-1.1.383-x86_64.rpm
rm rstudio-server-rhel-1.1.383-x86_64.rpm

#install shiny and shiny-server (2017-11-22)
R -e "install.packages('shiny', repos='https://cran.rstudio.com/')"

wget https://download3.rstudio.org/centos5.9/x86_64/shiny-server-1.5.5.872-rh5-x86_64.rpm

yum install -y --nogpgcheck shiny-server-1.5.5.872-rh5-x86_64.rpm

rm shiny-server-1.5.5.872-rh5-x86_64.rpm

#add user(s)

useradd xxxxx

echo xxxxx:yyyyy | chpasswd
```

Step 6)  Configuring the security group
In the EC2 launch wizard, we define a security group, which acts as a virtual firewall that controls the traffic for one or more instances. For our R-based analysis environment, we have to open up port 8787 for RStudio Server and port 3838 for Shiny Server.
We store it with name « rstats » for future instance launch.

Step 7) connect using a web browser to RStudio Server and R

<http://ec2-54-229-105-204.eu-west-1.compute.amazonaws.com:8787>

Step 8) Notes : commands for debugging/checking the EC2 instance launch in case of a problem :

SSH to the EC2 instance :

``` 
MacBook-Pro-de-Jean-Marc:AWS Solution Architect jmarc$ chmod 400 MyRServer.pem

MacBook-Pro-de-Jean-Marc:AWS Solution Architect jmarc$ ssh -i "MyRServer.pem" ec2-user@ec2-54-229-105-204.eu-west-1.compute.amazonaws.com

[ec2-user@ip-172-31-32-249 ~]$ curl http://169.254.169.254/latest/user-data

#!/bin/bash
#install R

yum install -y R

#install RStudio-Server 1.0.153 (2017-11-22)

wget https://download2.rstudio.org/rstudio-server-rhel-1.1.383-x86_64.rpm

yum install -y --nogpgcheck rstudio-server-rhel-1.1.383-x86_64.rpm

rm rstudio-server-rhel-1.1.383-x86_64.rpm

#install shiny and shiny-server (2017-11-22)

R -e "install.packages('shiny', repos='https://cran.rstudio.com/')"

wget https://download3.rstudio.org/centos5.9/x86_64/shiny-server-1.5.5.872-rh5-x86_64.rpm

yum install -y --nogpgcheck shiny-server-1.5.5.872-rh5-x86_64.rpm

rm shiny-server-1.5.5.872-rh5-x86_64.rpm

#add user(s)

useradd xxxxx

echo xxxxx:yyyyy | chpasswd [ec2-user@ip-172-31-32-249 ~]$


[ec2-user@ip-172-31-32-249 ~]$ cat /var/log/cloud-init-output.log
```


Step 9)  some R packages require installed Linux packages. So we install the curl-devel Linux package so that we can use the R package “RCurl”
```
ec2-user@ip-172-31-32-249 ~]$ sudo yum install curl-devel
```
Step 10) Configuring Shiny Server
```
[ec2-user@ip-172-31-32-249 ~]$ mkdir ~/ShinyApps

[ec2-user@ip-172-31-32-249 ~]$ sudo /opt/shiny-server/bin/deploy-example user-dirs

Attempting to deploy configuration named: user-dirs

Configuration file already exists at /etc/shiny-server/shiny-server.conf. Are you sure you want to overwrite it? [yN]y

Copied /opt/shiny-server/config/user-dirs.config to /etc/shiny-server/shiny-server.conf.

Installing sample apps...

/srv/shiny-server/index.html already exists. Not overwriting.

/srv/shiny-server/sample-apps already exists. Not overwriting.

Done installing samples.

Restarting Shiny Server...

shiny-server stop/waiting

Waiting...

shiny-server start/running, process 5677


The user-dirs config is all setup now. Enjoy!

[ec2-user@ip-172-31-32-249 ~]$ cp -R /opt/shiny-server/samples/sample-apps/hello ~/ShinyApps/
```


Step 11) Connect using a web browser to Shiny Server

<http://ec2-54-229-105-204.eu-west-1.compute.amazonaws.com:3838/ec2-user/hello/>

Now I can create your Shiny dashboards and deploy them via your ShinyApps folder. For advice on creating a Shiny dashboard, see the Teach Yourself Shiny tutorial.



Step 12) Create an AMI image for fast future launches
The easiest way to save an AMI with new modifications is to create the AMI image directly from the running instance.

From the AWS Management Console, click on the instance, then right-click Image -> Create Image.

From that dialog, set the Name, Description etc. Make sure to leave No Reboot unchecked. 



Step 13) Check Availability of the personal AMI in Images->AMI 

We can launch the instance from there.
Don’t forget to allocate a public access network and the  security group « rstats » as stored in step 6


Keep data storage:
In case you terminate the instance it will destroy the root EBS and its data, if instance was created with default behavior.
In case we don’t want to save another AMI image with the data, here is how I succeed it to retrieve my data with future instance of same AMI:
1)	I stop the instance
2)	I detach the EBS, and can terminate the instance.
3)	When create a new instance with my stored AMI, I launched it and stop it
4)	I detach the new root EBC and attach the old one instead
5)	I start the second instance again. It is know running with old data at same place, so readable with my username from RServer


