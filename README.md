# SonarQube Setup on EC2 with CI/CD Integration

## Project Overview

This project implements automated code quality analysis using SonarQube on AWS EC2 with GitHub Actions CI/CD integration for Spring PetClinic application.

## Objectives

- Deploy SonarQube server on AWS EC2 instance
- Set up automated code quality analysis via GitHub Actions
- Integrate SonarQube with Spring PetClinic application
- Implement Infrastructure as Code using Terraform

## What Was Done

### Infrastructure (Terraform)

- **EC2 Instance**: Ubuntu 24.04 LTS (t2.medium) in eu-central-1 region
- **Storage**: 30GB GP3 encrypted root volume
- **Security Group**: Configured for SSH (22), HTTP (80), HTTPS (443), and SonarQube (9000)
- **User Data**: Automated Docker installation on instance startup
- **Network**: Default VPC with public IP assignment

### CI/CD Pipeline (GitHub Actions)

- **Workflow**: SonarQube analysis triggered on pull requests
- **Target**: Spring PetClinic application (`spring-petclinic/`)
- **Build**: Maven-based build with JDK 21
- **Analysis**: Automated code quality checks sent to SonarQube server

### Application Integration

- Spring PetClinic application integrated into repository structure
- SonarQube project configured: `alex-project-ironhack`
- Source and test code separation for proper analysis

## Key Decisions

1. **Ubuntu 24.04 LTS**: Chose latest LTS for better Docker support and security updates
2. **Docker Installation via User Data**: Automated setup eliminates manual configuration steps
3. **Separate Source/Test Paths**: Fixed SonarQube indexing conflicts by explicitly separating `src/main` and `src/test`
4. **Pull Request Only Trigger**: Analysis runs only on PRs to avoid duplicate scans and reduce costs
5. **Default VPC**: Used AWS default VPC for simplicity, suitable for lab environment
6. **GitHub Secrets**: SonarQube credentials stored securely in GitHub Secrets

## Project Structure

```
.
├── infra/terraform/          # Infrastructure as Code
│   ├── main.tf              # EC2 instance, security groups, VPC
│   ├── variables.tf         # Terraform variables
│   ├── terraform.tfvars     # Configuration values
│   └── outputs.tf          # Output values
├── .github/workflows/       # CI/CD pipelines
│   └── sonar.yaml          # SonarQube analysis workflow
├── spring-petclinic/        # Spring PetClinic application
└── README.md               # This file
```

## Prerequisites

- AWS account with appropriate permissions
- GitHub repository with Secrets configured:
  - `SONAR_HOST_URL`: SonarQube server URL
  - `SONAR_TOKEN`: SonarQube authentication token
- Terraform >= 1.0
- AWS CLI configured

## Quick Start

1. **Deploy Infrastructure**:
   ```bash
   cd infra/terraform
   terraform init
   terraform plan -var-file=terraform.tfvars
   terraform apply
   ```

2. **Set up SonarQube**:
   - SSH to EC2 instance
   - Run SonarQube container: `docker run -d -p 9000:9000 sonarqube:latest`
   - Access SonarQube UI at `http://<ec2-public-ip>:9000`

3. **Configure GitHub Secrets**:
   - Add `SONAR_HOST_URL` and `SONAR_TOKEN` in repository settings

4. **Trigger Analysis**:
   - Create a pull request with changes to `spring-petclinic/`
   - GitHub Actions will automatically run SonarQube analysis

## Results

- ✅ Automated infrastructure provisioning with Terraform
- ✅ SonarQube server running on EC2
- ✅ CI/CD pipeline integrated with GitHub Actions
- ✅ Code quality analysis automated for Spring PetClinic
- ✅ Secure credential management via GitHub Secrets

## Notes

- SonarQube requires significant memory (minimum 2GB RAM recommended)
- EC2 instance type `t2.medium` (2 vCPU, 4GB RAM) is suitable for development/testing
- For production, consider using RDS for SonarQube database instead of embedded H2
