# Armageddon 1.2

## Architecture Diagram
[Link](https://lucid.app/lucidchart/13304afe-7041-47cb-accd-bc86b73cc645/edit?viewport_loc=-3750%2C-3853%2C7225%2C3438%2C0_0&invitationId=inv_4a28dc03-a6c4-4121-9ee8-3d04d2b28ba8)

## Scenario

Tokyo Midtown Medical Center (TMMC) aims to expand its medical care services in Japan by creating a J-Tele-Doctor system. This system is intended for customers who avoid visiting hospitals due to sickness or are located abroad. TMMC sees this as an opportunity to enhance their services ahead of the next pandemic.

**AWS Japan** has won the contract, and you are tasked as the Solution Architect. The application must be available internationally and support local languages due to the traveling nature of TMMC's customers.

## Stage One Tasks

### 1. Local Application Hosting

Local application hosting is required for Japanese and foreign customers in the following locations:
- Tokyo
- New York
- London
- São Paulo
- Australia
- Hong Kong
- California

### 2. Local Requirements

Each area must have:
- Auto Scaling Group (ASG) with a minimum of 2 Availability Zones (AZs).
- A minimum of 1 EC2 instance for the current test deployment.
- Deployment to a security zone for transferring syslog data, with the capability to transfer data to Japan.
- Limitation to port 80 open to the public.

### 3. Limitations

These must be observed and respected. Failure to adhere to these will result in automatic project failure.

#### A. Syslog Data

1. Syslog data must be stored in Japan only. The SIEM/Syslog server will be deployed in Stage 20.
2. Syslog server must be fault-tolerant.
3. Syslog server must be deployed as a basic EC2 instance for testing purposes.
4. Except for Tokyo, all other regions can only send data to the Syslog server; they cannot access the Syslog server.
5. Terraform output must contain the relevant artifacts enforcing A.3.

   **Solution**: Create a CloudWatch alarm based on the primary syslog server. Associate it with a health check and a DNS A record using DNS failover routing. The hosted zone is private and deployed in every relevant region.

#### B. Personal Information

- No personal information can be stored abroad and must remain within Japan's borders. Additionally, this data cannot be transferred via a VPN.

   **Solution**: Cross Regional Transit Gateway routing for Cross-Regional communication between the syslog agents and the syslog server in Japan.

#### C. Databases

- Databases will be deployed immediately. Basic Aurora MySQL/Postgres is acceptable.

#### D. Availability Zones

- The AZ containing syslog data must be limited to a private subnet. "Limited" means this AZ cannot have a public subnet.
- The AZ containing the database with PII must be limited to a private subnet. "Limited" means this AZ cannot have a public subnet.
- The database and Syslog server cannot reside in the same subnet.

---

