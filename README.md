# VPC-Peering
# VPC Peering Setup for AWS EC2 Instances

This guide explains how to set up VPC Peering between two different VPCs on AWS, enabling private communication between EC2 instances in isolated networks without going over the internet.

## 🎯 Objective
Establish secure, internal communication between two EC2 instances in separate VPCs by configuring VPC Peering, Security Groups, and Route Tables, and verifying the connection via SSH and ICMP ping.

## ✅ Prerequisites
- Two existing VPCs in the same region (or inter-region if supported)
- Subnets created in each VPC
- EC2 instances launched in each subnet
- Access to AWS Console with necessary permissions

## 🧰 Tools and Services Used

| Service         | Purpose                                      |
|----------------|----------------------------------------------|
| VPC            | Logical network isolation for EC2 instances  |
| EC2            | Hosts instances in separate VPCs             |
| VPC Peering    | Enables private communication between VPCs   |
| Route Tables   | Directs traffic between peered VPCs          |
| Security Groups| Controls ICMP and SSH access                 |

---

## ⚙️ Setup Steps

### 1. Launch EC2 Instances
1. Go to **EC2 Dashboard > Instances > Launch Instances**.
2. Launch one EC2 instance in each VPC, placing them in different subnets:
   - Choose **Amazon Linux 2** or **Ubuntu 20.04 LTS AMI**.
   - Select **t2.micro** instance type.
   - Under **Network settings**:
     - Select the respective VPC and subnet (e.g., vpc-a with subnet `10.0.1.0/24`, vpc-b with subnet `10.1.1.0/24`).
     - Enable **Auto-assign Public IP**.
   - Assign a **Key Pair** for SSH access.
3. Create or use a **Security Group** that allows:
   - **SSH (Port 22)** from your IP address.
4. Launch both instances.

### 2. Configure Security Groups
1. Go to **EC2 Dashboard > Security Groups**.
2. Edit **Inbound Rules** for both EC2 instances’ Security Groups:
   - Add **SSH (TCP, Port 22)** from your IP (e.g., `your-ip/32`).
   - Add **ICMP – Echo Request** from the CIDR block of the other VPC:
     - For VPC A’s instance: `10.1.0.0/16`
     - For VPC B’s instance: `10.0.0.0/16`
3. Save the rules.

### 3. Create VPC Peering Connection
1. Go to **VPC Dashboard > Peering Connections**.
2. Click **Create Peering Connection**.
3. Fill in:
   - **Peering connection name**: `vpc-a-to-vpc-b`
   - **Requester VPC**: Select first VPC (e.g., vpc-a)
   - **Accepter VPC**: Select second VPC (e.g., vpc-b)
4. Click **Create Peering Connection**.

### 4. Accept the Peering Connection
1. In **VPC Dashboard > Peering Connections**, locate the created connection.
2. Select it → Click **Actions > Accept Request**.
3. Verify the status changes to **Active**.

### 5. Update Route Tables
1. Go to **VPC Dashboard > Route Tables**.
2. For each VPC’s route table:
   - Select the route table associated with the EC2 instance’s subnet.
   - Click **Routes > Edit Routes** → Add:
     - **Destination**: CIDR block of the *other* VPC
     - **Target**: Peering Connection ID (e.g., `pcx-xxxxxxx`)
3. Save changes.

### 6. Verify Connectivity
1. SSH into the EC2 instance in VPC A using its public IP and key pair.
2. Inside the terminal, run:
   ```bash
   ping <Private-IP-of-EC2-in-VPC-B>
   ```
3. Confirm successful ping replies.
4. Optionally, repeat from the VPC B instance to VPC A’s private IP.

---

## 📋 Validation Checklist

| Checkpoint                                    | Status |
|----------------------------------------------|--------|
| EC2 instances launched in separate VPCs       | ☑️     |
| Public IPs assigned for initial SSH           | ☑️     |
| ICMP and SSH rules added to Security Groups   | ☑️     |
| VPC Peering connection created and accepted   | ☑️     |
| Route tables updated for both VPCs            | ☑️     |
| Private IP ping successful                    | ☑️     |

---

## 📌 Notes
- **Security**: After validation, remove public IPs and restrict SSH access further for production.
- **Monitoring**: Use **VPC Flow Logs** to monitor traffic.
- **Multi-Account Peering**: Ensure proper IAM permissions are in place.

---

## 🏷️ Tags
`#AWS` `#VPCPeering` `#CloudNetworking` `#DevOps` `#Infrastructure` `#CloudSecurity` `#AWSNetworking` `#SysOps` `#TechLearning` `#CloudJourney`

---

📄 **Reference Notes**: [Google Doc](https://docs.google.com/document/d/15HISvMNY7tdf2RBmfeFapb-_y9NmPyp9G39i9_eC5KM/edit?usp=sharing)
