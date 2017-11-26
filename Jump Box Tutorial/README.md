Jump Box Tutorial
-----------------

*Note: this is my first Markdown based document and I used the tutorial on <http://agea.github.io/tutorial.md/>*

**Problem Statement**

We have 2 subnet *S1* and *S2* in one VPC, with one EC2 instance *IA* in the subnet *SA* and one EC2 instance *IB* in the subnet *SB*.\
*SA* is public, *SB* is private.\
We want to be able to connect with SSH from Internet to *IA* but not *IB*.\
We want to connect with SSH to *IB* from *IA* (and only from *IA*).\
We want *IB* to ping any destination in the Internet, although it is in a private subnet and not reachable from Internet.

**Solution**

1) We create a the Virtual Private Cloud *VPC Jump Box* with the subnet 10.0.0.0/16
2) We create a the internet gateway *igw-JumpBox* and attach it to *VPC Jump Box*
3) In the route table associate to *VPC Jump Box* we create the default route 0.0.0.0/0 with next stop *igw-JumpBox*
4) We create the subnet *Public-JumpBox* with CIDR 10.0.1.0/24 (*SA* in our problem statement) and subnet *Private-JumpBox* with CIDR 10.0.2.0/24 (*SB* in our problem statement) 
5) We launch the public EC2 instance *Public EC2* attached to subnet *Public-JumpBox*, with automatic allocation of a public Internet address. We also accept SSH connextion (port TCP 22) from anywhere (0.0.0.0/0)
6) 5) We launch the private EC2 instance *Private EC2* attached to subnet *Private-JumpBox*, with no allocation of a public Internet address. We also accept SSH connextion (port TCP 22) from subnet *Public-JumpBox* (10.0.1.0/24) and accept to receive ping responses (ICMP) from anywhere (0.0.0.0/0).

Within the VPC we create the *SA* subnet 10.0.1.0/16 as a **public** subnet\
and the *SB* subnet 10.0.2.0/16 as **private** subnet\

