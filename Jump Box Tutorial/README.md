*Note: this is my first Markdown based document and I used the tutorial on <http://agea.github.io/tutorial.md/>*

**Course:** AWS Solution Architect Associate

Assignment: Jump Box Tutorial
-----------------------------

**Due date:** 28/11/2017\
**Author:** Jean-Marc Uzé\
**Version:** 1.1

**Problem Statement**

- We have 2 subnet *S1* and *S2* in one VPC, with one EC2 instance *IA* in the subnet *SA* and one EC2 instance *IB* in the subnet *SB*.\
*SA* is public, *SB* is private.\
- We want to be able to connect with SSH from Internet to *IA* but not *IB*.\
- We want to connect with SSH to *IB* from *IA* (and only from *IA*).\
- We want *IB* to ping any destination in the Internet, although it is in a private subnet and not reachable from Internet.

**Solution**

1) We create the Virtual Private Cloud *VPC Jump Box* with the subnet 10.0.0.0/16
2) We create the internet gateway *igw-JumpBox* and associate it to *VPC Jump Box*
3) In the route table associate to *VPC Jump Box* we create the default route 0.0.0.0/0 with next stop *igw-JumpBox*
4) We create the subnet *Public-JumpBox* with CIDR 10.0.1.0/24 (*SA* in our problem statement) and subnet *Private-JumpBox* with CIDR 10.0.2.0/24 (*SB* in our problem statement) 
5) We launch the public EC2 instance *Public EC2* associated to subnet *Public-JumpBox*, with automatic allocation of a public Internet address. We also accept SSH connextion (port TCP 22) from anywhere (0.0.0.0/0)
6) We launch the private EC2 instance *Private EC2* associated to subnet *Private-JumpBox*, with no allocation of a public Internet address. We also accept SSH connexion (port TCP 22) from subnet *Public-JumpBox* (10.0.1.0/24) and **accept to receive ping responses (ICMP) from anywhere (0.0.0.0/0)**.
7) We connect to *Public EC2* with SSH from local laptop, using the appropriate .pem key.\
8) on *Public EC2* we copy/paste the .pem key from our local laptop to a file with following commands:
```
echo "-----BEGIN RSA PRIVATE KEY-----
blablabla
-----END RSA PRIVATE KEY-----" > /home/ec2-user/MyRServer.pem

chmod 400 /home/ec2-user/MyRServer.pem
```
9) We can now connect from *Public EC2* to *Private EC2* using SSH with this .pem file (*MyRServer.pem* in the example above)
10) we create a NAT Gateway associated to subnet *Public-JumpBox* and to an Elastic IP address.
11) We create a new Route Table called *RT Private Jump Box* associated to subnet *Private-JumpBox*. We add following routes:
```
Destination   Target
10.0.0.0/16   Local
0.0.0.0/0     <the name of the just created nat gateway>
```
Note: the route table associated to subnet *Public-JumpBox* remains in the main route table of *VPC Jump Box*

12) we can now ping the internet from the *Private EC2* instance associated to subnet *Private-JumpBox*:
```
[ec2-user@ip-10-0-2-248 ~]$ ping www.google.com
PING www.google.com (108.177.98.103) 56(84) bytes of data.
64 bytes from pj-in-f103.1e100.net (108.177.98.103): icmp_seq=1 ttl=28 time=143 ms
64 bytes from pj-in-f103.1e100.net (108.177.98.103): icmp_seq=2 ttl=28 time=143 ms
64 bytes from pj-in-f103.1e100.net (108.177.98.103): icmp_seq=3 ttl=28 time=143 ms
^C
--- www.google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2002ms
rtt min/avg/max/mdev = 143.387/143.468/143.607/0.448 ms
```

**References**

NAT Gateways: <https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-nat-gateway.html>

