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

We create a VPC with the subnet 10.0.0.0/16\
Within the VPC we create the *SA* subnet 10.0.1.0/16 as a **public** subnet\
and the *SB* subnet 10.0.2.0/16 as **private** subnet\

