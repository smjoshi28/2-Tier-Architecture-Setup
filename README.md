2-Tier Architecture with RDS & Terraform

## 📌 Overview

This project demonstrates a production-ready, modularized AWS infrastructure deployment. It features a 2-tier architecture that separates the web layer (Flask on EC2) from the data layer (Managed MySQL on RDS) for enhanced security and scalability.

## 🏗 Architecture

Instead of a flat network, this project uses a tiered approach:

  * **Public Layer:** Internet Gateway, Public Subnets, and an EC2 Web Server.
  * **Private Layer:** Isolated Private Subnets across two Availability Zones for RDS.
  * **Security Layer:** Chained Security Groups where the DB only accepts traffic from the Web SG.

-----

## 📁 Project Structure

The project is split into reusable modules to follow DRY (Don't Repeat Yourself) principles:

```text
.
├── main.tf                 # Root orchestration
├── variables.tf            # Root input variables
├── outputs.tf              # Cross-module outputs
├── terraform.tfvars        # Local secrets (Git Ignored)
└── modules/
    ├── vpc/                # Networking: VPC, Subnets, IGW, Route Tables
    ├── security_groups/    # Firewall: Web (80/22) & DB (3306) rules
    ├── rds/                # Data: MySQL Instance & Subnet Groups
    ├── secrets/            # Security: Parameter Store/Secrets integration
    └── ec2/                # Compute: Ubuntu + Flask Bootstrapping
```

-----

## 🚀 Deployment Guide

### 1\. Prerequisites

  * Terraform v1.0+
  * AWS CLI configured with IAM permissions
  * A `terraform.tfvars` file (see Configuration section)

### 2\. Execution

```bash
# Initialize and download providers
terraform init

# Validate the configuration
terraform validate

# Review infrastructure changes
terraform plan

# Deploy to AWS
terraform apply -auto-approve
```

-----

## 🛠 Technical Deep Dive

### **Security & Networking**

  * **Zero Public Exposure:** The RDS instance is placed in a private subnet with `publicly_accessible = false`.
  * **Security Group Nesting:** The Database Security Group does not allow CIDR blocks; it specifically references the `web_sg_id`, ensuring only your app can talk to the DB.
  * **Sensitive Data:** Utilizes Terraform's `sensitive = true` flag for database credentials to mask them in console output.

### **Compute Bootstrapping**

The EC2 instance uses a `user_data.sh` script to:

1.  Install Python3, Pip, and MySQL client.
2.  Configure a Flask application environment.
3.  Inject the RDS Endpoint dynamically into the app's configuration.

-----

## 📊 Outputs & Validation

Once deployed, Terraform provides the following key information:

  * `application_url`: The public DNS to access your Flask app.
  * `rds_endpoint`: The internal address of the MySQL database.
  * `web_server_ip`: For administrative SSH access.

-----

## 🧹 Cleanup

To avoid unnecessary AWS costs, destroy the environment when finished:

```bash
terraform destroy
```

-----

## 🛡 License

Distributed under the MIT License. See `LICENSE` for more information.

-----

### Pro-Tip for your Portfolio:

Since you already pushed your code to GitHub successfully, make sure to:

1.  **Add your Eraser.io PNG** to the `images/` folder.
2.  **Double-check the filename** (GitHub is case-sensitive, so `architecture.png` is different from `Architecture.PNG`).
3.  **Update the `.gitignore`** to include `*.tfstate` and `terraform.tfvars` so you don't accidentally leak your AWS state or DB passwords\!

Does this structure work for your LinkedIn "Learning in Public" update?
