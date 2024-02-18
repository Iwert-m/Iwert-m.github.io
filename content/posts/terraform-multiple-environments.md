---
title: "Managing multiple environments using Terraform"
date: Feb 18, 2024
keywords: ["terraform", "infrastructure as code", "iac", "cloud"]
tags: ["terraform", "iac", "cloud", "git"]
socialShare: true
draft: true
---

Terraform is an amazing tool for managing your infrastructure.
It allows to define your infrastructure as code and manage it in a version control system.

Getting [started with Terraform]({{< ref "./introduction-to-terraform.md" >}} "Introduction to Terraform") is easy enough, but as your infrastructure grows, 
eventually there will be a need to manage multiple environments.



Throughout this post we will assume that you already have a CI/CD pipeline in place to deploy your infrastrucutre.
Either through a service that handles your deploys and your state file for you or through a self managed setup.

## Using different folders for different environments


#### Pros
+ Easy to understand
+ Easy to set up and manage

#### Cons
- Does not scale well
- Prone to human error 

## Using a Git branching strategy

This strategy is called [Gitlab flow](https://docs.gitlab.com/ee/topics/gitlab_flow.html#environment-branches-with-gitlab-flow).

Suppose that we have the following 3 environments that we want to manage using Terraform:

```goat
+--------------+       +------+      +------------+
| Developement +------>| Test +----->| Production |
+--------------+       +------+      +------------+
```

Changes should be promoted from dev -> test -> prod through a pull request.

// TODO
Each of these long-lived branches is tracked using a separate workspace in Terraform cloud. In Terraform cloud:

- Each workspace has a separate state file
- All workspaces are part of the same project: [the default one](#TODO)
- It is possible to have workspace-specific variables

#### Variables per environment

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
```

The lookup function allows the environment variable to be used as a dynamic key.
Optionally a local variable can be used for for readability.

```hcl
    locals {
        var_name = lookup(var.var_name, var.environment)
    }
```
