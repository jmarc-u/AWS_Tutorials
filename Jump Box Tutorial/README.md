Jump Box Tutorial
-----------------

*Note: this is my first Markdown based document and I used the tutorial on <http://agea.github.io/tutorial.md/>*

**Problem Statement**

We have 2 subnet in one VPC, with one EC2 instance IA in the subnet SA and one EC2 instance IB in the subnet SB.\
SA is public, SB is private.\
We want to be able to connect with SS from Internet to IA but not IB.\
We want to connect with SSH to IB from IA (only).\
We want IB to ping the Internet, although it is in a private subnet and not reachable from Internet.\

**Solution**

