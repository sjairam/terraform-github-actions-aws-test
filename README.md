# RKE2 Terraform Module on AWS

This README serves as an in-depth guide on how to deploy an RKE2 (Rancher Kubernetes Engine) cluster on AWS using a Terraform module. This module handles the configuration and deployment of the RKE2 server and agent nodes, along with supporting the installation of Rancher, ArgoCD, and Istio.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Codebase Overview](#codebase-overview)
- [Environmental Configuration](#environmental-configuration)
- [Step-by-Step Deployment Guide](#step-by-step-deployment-guide)
- [Accessing the Deployed Services](#accessing-the-deployed-services)

## Prerequisites

Before proceeding with the deployment of the RKE2 cluster, ensure you have the following prerequisites in place:

1. Terraform 

2. Terraform Cloud account

3. AWS Account & CLI: 


## Codebase Overview

The codebase for deploying RKE2 using Terraform is structured in a modular fashion to support code reuse and separation of concerns. Each module is designed to provide a distinct capability. The primary module is located in the `terraform-modules/rke2-install` directory. It consists of Terraform configuration files (`*.tf`), variables (`*.tfvars`), and templates for user data scripts (`templates/userdata_*.sh`).

The `terraform-modules/rke2-install/templates` directory contains shell scripts used in the setup of the server and agent nodes:

1. **userdata_agent.sh**: This script handles the setup for the RKE2 agent node. It updates the SSM agent, registers with the Satellite server, installs necessary packages, fetches the node token from the S3 bucket, and starts the RKE2 agent service.

2. **userdata_server.sh**: This script sets up the RKE2 server node. In addition to the setup performed by `userdata_agent.sh`, this script configures the Kubernetes control plane and installs Rancher, Helm, Cert-Manager, ArgoCD, and Istio.

## Environmental Configuration

The codebase design supports configuration flexibility across different environments. Each environment configuration resides in a dedicated directory under the `infrastructure/` directory. For example, the sandbox environment configuration is located in `infrastructure/sandbox`.

If you wish to configure a new environment, create a new directory under `infrastructure/` (e.g., `infrastructure/production`). In the new directory, create a `main.tf` file. This file should call the `rke2-install` module and pass in environment-specific variables.

## Step-by-Step Deployment Guide

To provision the infrastructure using this Terraform module, follow these steps:

1. **Initialize Terraform**

Navigate to your environment directory (e.g., `infrastructure/sandbox`), then initialize your Terraform working directory by running: terraform init

This command initializes a new or existing Terraform working directory by creating initial files, loading any remote state, downloading modules, etc.

2. **Plan Your Infrastructure**

Before applying changes, it's advisable to create an execution plan to reach the desired state of your infrastructure: terraform plan

The `terraform plan` command creates an execution plan. By default, creating a plan requires no extra options.

3. **Apply the Infrastructure Changes**

If the execution plan corresponds to your expectations, you can apply the changes: terraform apply

The `terraform apply` command executes the actions proposed in a Terraform plan.

## Accessing the Deployed Services

After Terraform successfully provisions your infrastructure, the following services can be accessed:

- **Rancher**: An open-source multi-cluster Kubernetes management platform. Access the Rancher UI at the domain specified during setup (e.g., `https://rancher.lib.harvard.edu/`). The password is stored in the AWS Secrets Manager under the key `harvard-lts-rke-rancher`.

- **ArgoCD**: A declarative, GitOps continuous delivery tool for Kubernetes. Access ArgoCD at `https://rancher.lib.harvard.edu:30446`,Username is admin The password is stored in the AWS Secrets Manager under the key `harvard-lts-rke-argocd`.

Remember to adhere to best practices for secret management when accessing your services to ensure the security of your setup.

## Deregistering and Cleaning Up Resources

You can run them as follows:

```bash
bash deregister.sh





