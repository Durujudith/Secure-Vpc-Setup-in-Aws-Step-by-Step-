# Secure-Vpc-Setup-in-Aws-Step-by-Step
Secure AWS VPC setup with public (192.168.10.0/25) and private (192.168.10.128/25) subnets. Includes Bastion Host, NAT Gateway, Internet Gateway, Route Tables, Security Groups, and NACLs. Ensures secure connectivity where public resources access the internet while private workloads remain protected.



A practical walkthrough for building a layered, secure AWS network architecture.  
✅ Includes: VPC, Public/Private Subnets, Bastion Host, NAT Gateway, Route Tables, IGW, NACLs, Security Groups, and EC2 setup.

The goal is to build a layered security model where sensitive workloads are protected in private subnets but can still access the internet safely when needed.



 📽 Video Demo
👉 [Watch here](https://youtu.be/Nv6XCZRrwvI?si=MYfZU5ynsXy8fYkL)


 📌 Project Overview
This guide walks through how to set up a secure Virtual Private Cloud (VPC) in AWS with the following:

- Public & Private Subnets  
- Route Tables  
- Internet Gateway (IGW)  
- NAT Gateway  
- Security Groups & Network ACLs (NACLs)  
- EC2 Instances for secure communication  



📌 Architecture Summary
- *VPC* → 192.168.10.0/24
- *Subnets*  
  - Public Subnet: 192.168.10.0/25 (internet-facing resources like Bastion/EKS workers)  
  - Private Subnet: 192.168.10.128/25 (sensitive workloads like DBs & apps)  

- *Route Tables*  
  - Public Route → Connected to *Internet Gateway*  
  - Private Route → Connected to *NAT Gateway*  

- *Security Layers*  
  - *Security Groups* → Instance-level firewall (e.g., allow SSH only from my IP)  
  - *NACLs* → Subnet-level firewall (different rules for public vs private subnet)  

- *Connectivity*  
  - Internet Gateway → For inbound/outbound internet access (public subnet)  
  - NAT Gateway → For outbound-only internet access (private subnet)  



 🔑 Key Steps

VPC
- *CIDR Block:* 192.168.10.0/24

 Subnets
- *Public Subnet:* 192.168.10.0/25 (Auto-assign Public IP: ✅ Enabled)  
- *Private Subnet:* 192.168.10.128/25

 Internet Gateway (IGW)
- Created and attached to the VPC  

 Route Tables
- *Public Route Table* → 0.0.0.0/0 → IGW (associated with Public Subnet)  
- *Private Route Table* → 0.0.0.0/0 → NAT Gateway (associated with Private Subnet)  

 NAT Gateway
- Deployed in *Public Subnet*  
- Attached with *Elastic IP*  

 Security Groups
- *Inbound:* Allow SSH (port 22) only from my IP  
- *Outbound:* Allow all traffic  
- Applied to *Bastion Host* and *Private EC2*  

Network ACLs (NACLs)
- *Public NACL*  
  - Inbound: Allow SSH + all traffic (temporary)  
  - Outbound: Allow all traffic  

- *Private NACL*  
  - Inbound: Allow SSH from Public Subnet, MySQL (3306) from VPC CIDR  
  - Deny all other inbound traffic  
  - Outbound: Allow all traffic  

 EC2 Instances
- *Bastion Host:* In Public Subnet, SSH allowed from internet (my IP)  
- *Private Instance:* In Private Subnet, accessible only via Bastion (SSH jump)  



 ✅ Result
Infrastructure deployed securely with segmented public and private access, enabling safe remote administration and private service hosting.



 🔧 Troubleshooting

 1. Error: Permission denied (public key)  
- *Cause:* The private key file wasn’t in the folder at all.  
- *Fix:* I recreated the file by running nano key.pem, pasted in the key content, and saved it.  

 2. Error: Invalid/incorrect key  
- *Cause:* While copying, I had accidentally missed a line of the key.  
- *Fix:* I fixed this offline by re-adding the missing line and then re-uploaded it.  

 3. Permissions issue  
- After fixing the key, AWS still blocked access until I set correct permissions.  
- *Solution:*  
  ```bash
  chmod 600 key.pem   # temporary while fixing the key
  chmod 400 key.pem   # final required permission

  *Why this matters* → chmod 400 ensures only the owner can read the key file, which is an AWS security requirement for SSH.

✅ Connectivity Verification
 –SSH into public instance (Bastion Host)
–From Bastion, SSH into private instance
–Ping test between instances shows internal routing works
–Security groups and NACLs restrict/allow traffic correctly
 


 🎯 Outcome
We built a secure, production-ready VPC setup that:

–Enables secure internet access for public resources
–Keeps private resources shielded but connected
–Uses a multi-layered security model (SGs + NACLs)
–Provides a foundation for scalable architectures like EKS, ECS, Web apps, or Database 

This is the *foundation of AWS networking* for secure workloads like EKS clusters, web apps, and databases.  



