# Terraform AWS VPC from Scratch

Build a production-ready AWS VPC with public and private subnets, NAT Gateways, and complete networking infrastructure using Terraform.

## 🏗️ Architecture

This project creates a highly available VPC with:
- Custom VPC with configurable CIDR block
- Public subnets in multiple Availability Zones
- Private subnets in multiple Availability Zones
- Internet Gateway for public subnet internet access
- NAT Gateways for private subnet outbound internet access
- Route tables for traffic management
- Network ACLs for subnet-level security
- Optional VPC Flow Logs for traffic monitoring

See [vpc-architecture.md](vpc-architecture.md) for detailed architecture diagram.

## ✨ Features

- ✅ Multi-AZ setup for high availability
- ✅ Separate NAT Gateway per AZ (no single point of failure)
- ✅ Customizable CIDR blocks
- ✅ Public and private subnet separation
- ✅ Proper route table configuration
- ✅ Network ACLs for additional security
- ✅ Optional VPC Flow Logs
- ✅ Cost optimization options (NAT Gateway can be disabled)

## 📋 Prerequisites

- AWS Account
- [Terraform](https://www.terraform.io/downloads.html) installed (>= 1.0)
- [AWS CLI](https://aws.amazon.com/cli/) configured with credentials
- Basic understanding of AWS networking (VPC, subnets, routing)

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone <your-repo-url>
cd terraform-aws-vpc-from-scratch
```

### 2. Configure Variables

Copy the example variables file and customize:

```bash
cp terraform.tfvars.example terraform.tfvars
```

Edit `terraform.tfvars`:

```hcl
project_name = "my-vpc-project"
environment  = "dev"
vpc_cidr     = "10.0.0.0/16"

public_subnet_cidrs  = ["10.0.1.0/24", "10.0.2.0/24"]
private_subnet_cidrs = ["10.0.11.0/24", "10.0.12.0/24"]

enable_nat_gateway = true   # Set to false to save costs in dev
enable_flow_logs   = false  # Enable for traffic monitoring
```

### 3. Initialize Terraform

```bash
terraform init
```

### 4. Preview Changes

```bash
terraform plan
```

### 5. Deploy Infrastructure

```bash
terraform apply
```

Type `yes` when prompted.

### 6. View Outputs

```bash
terraform output
```

## 📁 Project Structure

```
.
├── main.tf                    # Main VPC infrastructure
├── variables.tf               # Input variables
├── outputs.tf                 # Output values
├── terraform.tfvars.example   # Example configuration
├── .gitignore                # Git ignore rules
├── vpc-architecture.md       # Architecture diagram
└── README.md                 # This file
```

## 🔧 Configuration

### Variables

| Variable | Description | Type | Default |
|----------|-------------|------|---------|
| `aws_region` | AWS region | string | us-east-1 |
| `project_name` | Project name for tagging | string | my-vpc-project |
| `environment` | Environment (dev/staging/prod) | string | dev |
| `vpc_cidr` | VPC CIDR block | string | 10.0.0.0/16 |
| `public_subnet_cidrs` | Public subnet CIDR blocks | list(string) | ["10.0.1.0/24", "10.0.2.0/24"] |
| `private_subnet_cidrs` | Private subnet CIDR blocks | list(string) | ["10.0.11.0/24", "10.0.12.0/24"] |
| `enable_nat_gateway` | Enable NAT Gateway | bool | true |
| `enable_flow_logs` | Enable VPC Flow Logs | bool | false |

### Outputs

| Output | Description |
|--------|-------------|
| `vpc_id` | VPC ID |
| `vpc_cidr` | VPC CIDR block |
| `public_subnet_ids` | Public subnet IDs |
| `private_subnet_ids` | Private subnet IDs |
| `nat_gateway_ids` | NAT Gateway IDs |
| `nat_gateway_ips` | NAT Gateway Elastic IPs |
| `internet_gateway_id` | Internet Gateway ID |
| `availability_zones` | Availability Zones used |

## 💰 Cost Estimate

### With NAT Gateway (default)
- **NAT Gateway**: ~$0.045/hour = ~$32.40/month per NAT Gateway
- **Data Processing**: ~$0.045/GB processed
- **2 NAT Gateways**: ~$64.80/month
- **Elastic IPs**: Free while attached to running resources

**Total**: ~$65-75/month (depending on traffic)

### Without NAT Gateway (cost-optimized)
- **VPC, Subnets, Route Tables**: Free
- **Internet Gateway**: Free
- **VPC Flow Logs**: ~$0.50/GB ingested (if enabled)

**Total**: Nearly free (just Flow Logs if enabled)

### Cost Optimization Tips
- Set `enable_nat_gateway = false` for development environments
- Private subnets won't have internet access without NAT Gateway
- Use a single NAT Gateway for cost savings (less HA)
- Enable Flow Logs only when debugging network issues

## 🎯 Use Cases

This VPC is perfect for:

1. **Web Applications**
   - Load balancers in public subnets
   - Application servers in private subnets
   - Databases in private subnets

2. **Microservices**
   - API Gateway in public subnet
   - Services in private subnets
   - Secure inter-service communication

3. **Data Processing**
   - Bastion hosts in public subnet
   - Processing servers in private subnets
   - Databases in private subnets

## 🔐 Security Best Practices

This project implements:

✅ Private subnets for sensitive resources  
✅ NAT Gateway instead of NAT instances  
✅ Network ACLs for subnet-level security  
✅ Separate route tables per private subnet  
✅ No direct internet access to private resources  
✅ VPC Flow Logs for monitoring (optional)

### Additional Security Recommendations

- Add Security Groups for EC2 instance-level security
- Enable AWS GuardDuty for threat detection
- Use VPC endpoints for AWS service access
- Enable AWS CloudTrail for API logging
- Use AWS Config for compliance monitoring

## 📚 What You'll Learn

- Creating a custom VPC from scratch
- Designing multi-AZ architecture for high availability
- Understanding public vs private subnets
- Configuring Internet Gateway and NAT Gateway
- Managing route tables and routing
- Using Network ACLs for security
- Implementing VPC Flow Logs
- Terraform best practices for networking

## 🧪 Testing the VPC

After deployment, you can test by:

1. **Launch an EC2 instance in public subnet**
   ```bash
   # Should have internet access directly
   # Should be reachable from internet (if security group allows)
   ```

2. **Launch an EC2 instance in private subnet**
   ```bash
   # Should have internet access via NAT Gateway
   # Should NOT be directly reachable from internet
   ```

3. **Check routing**
   ```bash
   aws ec2 describe-route-tables --filters "Name=vpc-id,Values=<vpc-id>"
   ```

## 🔄 Modifying the VPC

### Add More Subnets

Edit `terraform.tfvars`:

```hcl
public_subnet_cidrs = [
  "10.0.1.0/24",
  "10.0.2.0/24",
  "10.0.3.0/24"  # Add third subnet
]

private_subnet_cidrs = [
  "10.0.11.0/24",
  "10.0.12.0/24",
  "10.0.13.0/24"  # Add third subnet
]
```

Then run:
```bash
terraform plan
terraform apply
```

### Change CIDR Blocks

**Warning**: Changing CIDR blocks will recreate the VPC and all resources!

```hcl
vpc_cidr = "172.16.0.0/16"  # Different CIDR range
```

### Disable NAT Gateway (Save Costs)

```hcl
enable_nat_gateway = false
```

**Note**: Private subnets will lose internet access.

## 🧹 Cleanup

To destroy all resources:

```bash
terraform destroy
```

Type `yes` when prompted.

**Important**: Make sure to:
1. Terminate all EC2 instances in the VPC first
2. Delete all ENIs (Elastic Network Interfaces)
3. Remove any other resources using the VPC

## 🐛 Troubleshooting

### Error: "subnet_id not found"
- Make sure you're using the correct subnet IDs from outputs
- Verify the VPC has been created successfully

### NAT Gateway creation timeout
- NAT Gateways can take 3-5 minutes to create
- Wait for the operation to complete
- Check AWS Console for status

### Cannot destroy VPC
- Ensure all EC2 instances are terminated
- Delete any Lambda functions in the VPC
- Remove all ENIs
- Check for RDS instances or other dependencies

### "CIDR block overlaps"
- Ensure your CIDR blocks don't overlap with existing VPCs
- Use different IP ranges for each subnet

## 📖 Additional Resources

- [AWS VPC Documentation](https://docs.aws.amazon.com/vpc/)
- [Terraform AWS Provider - VPC](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc)
- [AWS VPC Best Practices](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-best-practices.html)
- [CIDR Calculator](https://www.ipaddressguide.com/cidr)

## 🎓 Learning Path

After mastering this VPC setup, consider:

1. **Add Security Groups** - Instance-level firewalls
2. **Add VPC Endpoints** - Private access to AWS services
3. **Add VPN Gateway** - Connect to on-premises network
4. **Add Transit Gateway** - Connect multiple VPCs
5. **Add Network Firewall** - Advanced traffic filtering

## 🔗 Related Projects

This VPC can be used as a foundation for:

- [EC2 Web Server Project](../terraform-ec2-web-server)
- [Multi-Tier Application](../terraform-multi-tier-app)
- [ECS Container Deployment](../terraform-ecs-containers)

## 💡 Pro Tips

1. **CIDR Planning**: Plan your CIDR blocks carefully before deployment
   - Leave room for growth
   - Document your IP allocation
   - Avoid overlapping ranges

2. **Tagging Strategy**: Use consistent tags for cost tracking
   ```hcl
   tags = {
     Environment = "production"
     CostCenter  = "engineering"
     ManagedBy   = "terraform"
   }
   ```

3. **State Management**: Use remote state for team collaboration
   ```hcl
   terraform {
     backend "s3" {
       bucket = "my-terraform-state"
       key    = "vpc/terraform.tfstate"
       region = "us-east-1"
     }
   }
   ```

4. **Module Approach**: Convert this to a reusable module
   ```
   module "vpc" {
     source = "./modules/vpc"
     
     project_name = "my-app"
     vpc_cidr     = "10.0.0.0/16"
   }
   ```

## 📊 VPC Flow Logs Analysis

If you enabled Flow Logs, analyze them with:

```bash
# View logs in CloudWatch
aws logs tail /aws/vpc/my-vpc-project-flow-logs --follow

# Filter for rejected traffic
aws logs filter-log-events \
  --log-group-name /aws/vpc/my-vpc-project-flow-logs \
  --filter-pattern "REJECT"
```

## 🤝 Contributing

Found an issue or have an improvement? Feel free to open an issue or pull request!

## 📝 License

MIT License - Use this for learning and production projects!

---

**Happy Networking! 🌐**

If you found this helpful, please give it a ⭐ on GitHub!

## 📞 Support

If you have questions:
1. Check the troubleshooting section
2. Review AWS VPC documentation
3. Open an issue on GitHub

---

### Quick Reference Commands

```bash
# Initialize
terraform init

# Format code
terraform fmt

# Validate configuration
terraform validate

# Plan changes
terraform plan

# Apply changes
terraform apply

# Show current state
terraform show

# List resources
terraform state list

# Get specific output
terraform output vpc_id

# Destroy everything
terraform destroy
```
