---
date: 2024-09-20 
categories:
- Terraform  
readtime: 15 
authors:
- rolo  
---

# `trivy` Automating Security Scanning Applied to Terraform Resources

## Introduction
**Security is a vital concern when managing infrastructure**, and it’s critical to identify vulnerabilities in both container images and infrastructure-as-code (IaC). While Terraform helps automate the deployment of cloud resources, combining it with security tools like `trivy` ensures that any configuration or resource vulnerabilities are caught early.

In this post, we will walk through how to integrate `trivy` into your Terraform workflow to automate security scanning of the resources you define. We will cover setting up `trivy`, running scans, and interpreting the results, ensuring your Terraform-managed infrastructure is as secure as possible.

## Use case 
It’s important to recognize that [Trivy](https://aquasecurity.github.io/trivy/v0.17.2/) is a versatile security tool capable of scanning a wide range of resources, including container images, file systems, and repositories. However, in this post, we will focus specifically on scanning Infrastructure as Code (IaC) through Terraform configuration, utilizing Trivy’s [misconfiguration scanning mode]((https://aquasecurity.github.io/trivy/v0.55/tutorials/misconfiguration/terraform/)).

The Terraform configuration scanning feature is accessible through the `trivy config command`. This command performs a comprehensive scan of all configuration files within a directory to detect any misconfiguration issues, ensuring your infrastructure is secure from the start. You can explore more details on misconfiguration scans within the Trivy documentation, but here we’ll focus on two primary methods: scanning Terraform plans and direct configuration files.

### Method 1: Scanning with a Terraform Plan
The first method involves generating a Terraform plan and scanning it for misconfigurations. This allows Trivy to assess the planned infrastructure changes before they are applied, giving you the opportunity to catch issues early.

```bash 
cd $DESIRED_PATH
terraform plan --out tfplan
trivy config tfplan
```
- The `terraform plan --out tfplan` command creates a serialized Terraform plan file.
- `trivy config tfplan` then scans this plan for any potential security risks, providing insights before applying the configuration.


### Method 2: Scanning Configuration Files Directly
Alternatively, you can scan the Terraform configuration files directly without generating a plan. This is useful when you want to perform quick checks on your existing code or infrastructure definitions.

```bash
cd $DESIRED_PATH
trivy config ./ 
```

This command instructs Trivy to recursively scan all Terraform files in the specified directory, reporting any misconfigurations found.

## Trivy installation
For installation instructions please refer to the [oficial documentation](https://aquasecurity.github.io/trivy/v0.55/getting-started/installation/)

## See it in action
<p align="center">
  <a href="https://asciinema.org/a/dyysX0qf7W97rdZrKVtoIv6pL">
  <img src="https://asciinema.org/a/dyysX0qf7W97rdZrKVtoIv6pL.png" width="885"></image>
  </a>
</p>

## Automating the Scans in a CI/CD Pipeline

A good strategy is integrating trivy scans into your CI/CD pipeline. As an example we can expose it through github Actions, the official action can be found [here](https://github.com/aquasecurity/trivy-action),but as an easy alternative this pipe can be definied:

```bash
# GitHub Actions YAML file
name: Terraform Security Scanning

on: [push]

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Install trivy
      run: |
        curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sudo sh

    - name: Run trivy scan
      run: trivy config --severity HIGH,CRITICAL --exit-code 1 .

```

## Conclusion
Summarize the importance of security scanning in the Terraform workflow and how using trivy automates this process. Encourage readers to integrate scanning tools into their infrastructure deployments for proactive vulnerability management.