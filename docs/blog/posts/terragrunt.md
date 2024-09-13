---
date: 2024-09-10  
categories:
- Terraform  
readtime: 20 
authors:
- rolo  
---

# `Terragrunt` Raise the DRY flag 
If you're familiar with Infrastructure as Code (IaC) tools, this post is for you. The goal here is to introduce you to `Terragrunt`, a tool that enables you to follow the DRY (Don't Repeat Yourself) principle, making your Terraform code more maintainable and concise.

## What it is?
`Terragrunt` is a flexible orchestration tool designed to scale Infrastructure as Code, making it easier to manage Terraform configurations across multiple environments.

Let's present the problem.

## Keep your backend configuration DRY
Before diving into `Terragrunt`, let's first define the problem it solves. Consider the following Terraform project structure:

```bash 
#./terraform/
.
├── envs
│   ├── dev
│   │   ├── backend.tf
│   │   └── main.tf
│   ├── prod
│   │   ├── backend.tf
│   │   └── main.tf
│   └── stage
│       ├── backend.tf
│       └── main.tf
└── modules
    └── foundational
        └── main.tf
```

In this scenario, we have a foundational module, with separate environments for dev, stage, and prod. As the complexity of the system grows, maintaining repeated backend configurations becomes more challenging.

Take, for example, the following backend configuration for the dev environment:

```bash
# ./terraform/envs/dev/backend.tf
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "envs/dev/bakcend/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "my-lock-table"
  }
}
```

This configuration is required for each environment (`dev`, `stage`, `prod`), and you'll find yourself copying the same code across all of them. This approach isn't scalable and quickly becomes difficult to maintain.

Now, let's see how `Terragrunt` simplifies this.

### Introducing Terragrunt 
With `Terragrunt`, you can move repetitive backend configurations into a single file and reuse them across all environments. 

Here's how your updated directory structure looks:

```bash 
# ./terraform/
.
├── envs
│   ├── dev
│   │   ├── backend.tf
│   │   ├── main.tf
│   │   └──terragrunt.hcl
│   ├── prod
│   │   ├── backend.tf
│   │   ├── main.tf
│   │   └── terragrunt.hcl
│   ├── stage
│   │   ├── backend.tf
│   │   ├── main.tf
│   │   └── terragrunt.hcl
└── terragrunt.hcl
```

The `terragrunt.hcl` file uses the same HCL language as Terraform and centralizes the backend configuration. Instead of duplicating code, we now use Terragrunt’s `path_relative_to_include()` function to dynamically set the backend key for each environment. 

Here’s what that looks like:

```bash 
#./terraform/envs/terragrunt.hcl
remote_state {
  backend = "s3"
  generate = {
    path      = "backend.tf"
    if_exists = "overwrite_terragrunt"
  }
  config = {
    bucket = "my-terraform-state"

    key = "${path_relative_to_include()}/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "my-lock-table"
  }
}
```

By centralizing this, you only need to update the root `terragrunt.hcl` to apply changes across all environments.

### Including root configuration
You can include the root configuration in each child environment by referencing the root `terragrunt.hcl` file like this:

``` bash
#./terraform/env/stage/terragrunt.hcl
include "root" {
  path = find_in_parent_folders()
}
```

This drastically reduces duplication and keeps your backend configurations DRY.

## Keep your provider configuration DRY
One common challenge in managing Terraform configurations is dealing with repetitive provider blocks. For example, when you're configuring AWS provider roles, you often end up with the same block of code repeated across multiple modules:

```bash
# ./terraform/env/stage/main.tf
provider "aws" {
  assume_role {
    role_arn = "arn:aws:iam::0123456789:role/terragrunt"
  }
}
```
To avoid copy-pasting this configuration in every module, you can introduce Terraform variables:

```bash
# ./terraform/env/stage/main.tf
variable "assume_role_arn" {
  description = "Role to assume for AWS API calls"
}

provider "aws" {
  assume_role {
    role_arn = var.assume_role_arn
  }
}
```

This approach works fine initially, but as your infrastructure grows, maintaining this configuration across many modules can become tedious. For instance, if you need to update the configuration (e.g., adding a `session_name` parameter), you would have to modify every module where the provider is defined.

### Simplify with Terragrunt

Terragrunt offers a solution to this problem by allowing you to centralize common Terraform configurations. Like with backend configurations, you can define the provider configuration once and reuse it across multiple modules. Using Terragrunt’s generate block, you can automate the creation of provider configurations for each environment.

Here’s how it works:


```bash
#./terraform/env/stage/terragrunt.hcl
generate "provider" {
  path = "provider.tf"
  if_exists = "overwrite_terragrunt"
  contents = <<EOF
provider "aws" {
  assume_role {
    role_arn = "arn:aws:iam::0123456789:role/terragrunt"
  }
}
EOF
}
```

This generate block tells Terragrunt to create a provider.tf file in the working directory (where Terragrunt calls Terraform). The provider.tf file is generated with the necessary AWS provider configuration, meaning you no longer need to manually define this in every module.

When you run a Terragrunt command like terragrunt plan or terragrunt apply, it will generate the provider.tf file in the local .terragrunt-cache directory for each module

```bash 
$ cd /terraform/env/stage/
$ terragrunt apply
$ find . -name "provider.tf"
.terragrunt-cache/some-unique-hash/provider.tf
```
This approach ensures that your provider configuration is consistent and automatically injected into all relevant modules, saving you time and effort while keeping your code DRY.

By centralizing provider configurations with Terragrunt, you reduce the risk of errors from manual updates and ensure that any changes to provider settings are automatically applied across all modules.

## Installation     
For installation instructions, please refer to the [official documentation](https://terragrunt.gruntwork.io/docs/getting-started/install/)

