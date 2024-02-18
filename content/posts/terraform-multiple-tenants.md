---
title: "Managing multiple tenants using Terraform"
date: Feb 18, 2024
keywords: ["terraform", "infrastructure as code", "iac", "cloud"]
tags: ["terraform", "iac", "cloud", "git"]
socialShare: true
draft: true
---

Terraform is an amazing tool for managing your infrastructure.
It allows to define your infrastructure as code and manage it in a version control system.

Getting [started with Terraform]({{< ref "./introduction-to-terraform.md" >}} "Introduction to Terraform") is easy enough, but as your infrastructure grows, 
you will eventually need to manage multiple tenants.

Throughout this post we will assume that you already have a CI/CD pipeline in place to deploy your infrastrucutre.
Either through a service that handles your deploys and your state file for you or through a self managed setup.

## Using different folders for different tenants


#### Pros
+ Easy to understand
+ Easy to set up and manage

#### Cons
- Does not scale well
- Prone to human error 

## Using a Git branching strategy

### Variables per environment

Each workspace holds an `environment` variable that is made available during runs. This variable can be used to create environment-specific blocks with the help of the [lookup function](https://developer.hashicorp.com/terraform/language/v1.1.x/configuration-0-11/interpolation#lookup-map-key-default).

```hcl
    variable "var_name" {
        type = map(string)
        description = ""
        default = {
                dev     = "dev_value"
                prod    = "prod_value"
        }
    }
    # The lookup function allows the environment variable 
    # to be used as a dynamic key
    # And optionally use locals for readability
    locals {
        var_name = lookup(var.var_name, var.environment)
    }
```

