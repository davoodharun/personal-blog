---
title: "My Title"
description: "Awesome description"
layout: post
toc: false
comments: true
image: images/some_folder/your_image.png
hide: false
search_exclude: true
categories: [fastpages, jupyter]
metadata_key1: metadata_value1
metadata_key2: metadata_value2
---
# Optimizing Terraform projects with Terragrunt and a Terraform Registry

## Intro

Deploying your cloud computing infrastructure from code is becoming an industry standard for enterprise applications in order for them to scale effectively in large development teams.

Terraform  is an infrastructure as code tool that lets you define both cloud and on-prem resources using human-readable configuration files that you can version, reuse, and share across your various projects and development teams.

This post will focus on several methods and patterns to take Terraform even further, specifically around keeping your infrastructure code and configurations organized and easy to maintain. As a result, this post assumes you've worked with Terraform before and have a general understanding of how to use it and how it works. If you want to know more about Terraform and what it can offer, take a look at Hashicorp's [website]().


## Terraform Registry
Terraform has a few different offerings that each provide different features. The most basic of which is open-source, the Terraform CLI. Although Terraform CLI is a great tool on its own, it is significantly more useful when best practices are implemented. 

One of the very first things you should do is modularize your terraform code by breaking it apart into child modules that encapsulate smaller pieces of your infrastructure;  instead of having one main.tf file that provisions all of your resource types, break up your architecture into several components (i.e. appserviceplan, appservice, storage account, redis cache) and reference them in your encapsulating module (ex. main.tf). 

By default, Hashicorp gives you access to their public Terraform registry for various providers like AWS and Azure. The public registry already has re-usable modules for the simplest cloud resource blocks for you to extend and utilize.

However, although these pre-defined modules exist, in order to standardize things like naming conventions, cloud computing sizing/scaling, and other restrictions you may want to impose on your development teams, you should create your own modules that utilize modules in the public Terraform Registry with distinctly defined inputs and outputs.

Now the question arises: where do I keep the code for my child modules so that they are easily distributable, re-usable, and maintaiable. Creating these child modules and storing the code within your application code repository creates some disadvantages. What if you want to re-use these sub modules from other repositories or different projects? You'd have to duplicate the child module code and track it in several places. Not good.

To address this, you should utilize a shared registry or storage location for your child modules so that you can reference them from multiple repositories and even distribute different versions to different projects. This would involve moving each sub module to its own individual repo to be maintained independently and then uploading it to your registry or central storage location. Acceptable methods are:
-GitHub
-Bitbucket
-Generic Git, Mercurial repositories
-HTTP URLs
-S3 buckets
-GCS buckets

See the [Terraform documentation on module sources](https://www.terraform.io/language/modules/sources) for more information. 

Utilizing these methods allow you to maintain your sub modules independently, and distribute different versions that larger applications can choose to inherit. You might go from terraform projects like this:
![](./img/pre_modular.png)

to  this:

![](./img/modular.png)




## Terragrunt

Utilizing registries to modularize your infrastructure is only a small part of the improvements you can make to your Terraform code.

In Terraform, you need to define a "remote state" for each grouping of infrastructure you are trying to deploy/provision. This remote state could be an S3 bucket or an Azure Storage account, or Terraform Cloud. This is how Terraform knows where to track the state of your infrastructure to determine if any changes need to be applied or if the configuration has drifted away from baseline that is defined in your source code. 

In addiiton, you have to keep in mind that when your development teams and applications grow in size, you will frequently need to manage development, testing, quality assurance, and production environments for several projects/components simultaneously. 

How do you maintain all of this infrastructure reliably? Do you track all of the applications for one tier in one statefile? Or do you break them up and track them separately?

How do you efficiently share variables across environments and applications? How do you effectively apply these terraform projects in a continuous deployment pipeline in a way that is consisent and repeatable for different types of terraform projects?


At my current client, I was heavily involved with onboarding Terraform as an infrastructure as code (iac) tool. However, I ran into many challenges when trying to deploy multiple tiers (dev, test, stage, production etc.), especially in a continuous deployment pipeline.

The client I work for has the following requirements for one part of the services they offer (consider Azure Cloud provider for context):
- each tier has *six applications* for different areas of the United states
- *Each application* has a web server, a redis cache, app service plan, a storage account, and a key vault access policy to access a central key store
- Development and test tiers are deployed to a single region
- Stage and production environments are deployed to two regions, east and central

My client also had several other projects that needed similar architectural setups; most projects involved deploying six instances of the application (for each service area of the Unites States), each of which was configured differently through application settings.

Even though this seems like a complicated environment, the lessons learned from 



With just using the Terraform CLI, you might have sepearte ```backend.tf``` files for each tier and pass them dynamically to ```terraform init --backend-config=<PATH>```. You also might pass variable files dynamically to ```terraform plan --var-file=<PATH>``` to combine common and tier specific variable files. 

Terragrunt proides a gre

The methods above are great for modularizing your Terraform code in terms of the 
When deploying multiple tiers of environments with Terraform, Terragrunt can help you keep your Terraform code and remote state configurations DRY (DO NOT REPEAT YOURSELF).

Terragrunt is a minimal wrapper around Terraform that allows you to organize a heirarichal folder structure that dynamically assign and inherit remote state configurations, provider information, local variables, and module inputs for your terraform modules.


Modules in Package Sub-directories

##

## Features
### Remote State Configurations

### Basic Terraform Example




### Architecture

# Working with Terraform
