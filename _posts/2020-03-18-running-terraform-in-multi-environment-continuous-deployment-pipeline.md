---
layout: post
categories: [Tech, Azure]
tags: ['Terraform', 'DevOps', 'InfrastructureAsCode', 'Cloud Computing']
---

Terraform is an enabler for following Infrastructure As Code (IaC) principle which lets you track , manage & provision cloud infrastructure for multiple clouds .

We decided to add terraform automation as part of our continuous delivery/deployment pipeline . While implementing this for multi environment (dev/staging/production) deployment , we learnt a lot and thought of sharing some points which will help you design/implement if you have similar use case.

+ **Version**  
Install the same terraform version on deployment pipeline which was used for testing the terraform changes locally and keep them consistent throughout all environments.  

+ **Backend**  
Use remote_state feature of terraform to keep state files stored in centralized location like AWS S3 . Use TF_CLI_ARGS_init variable to manage different state files for different environments.  

+ **Variables**    
Terraform looks for all environment variables having a prefix TF_ . We use it for multiple cases like:  
     + Terraform variable values.
     + TF_WORKSPACE to create/select workspace.
     + TF_CLI_ARGS_init for setting terraform remote state per environment.  
          TF_CLI_ARGS_init=-backend-config="bucket=bucket-name"

+ **Workspace**  
Use terraform workspace feature for multi environments like respective workspace for respective environment (dev/staging/production)

+ **Auto Approval**  
Use the auto approval switch on terraform apply if you are confident with your Pull Request review process and validations running as part of pipeline.  
`terraform apply -auto-approve`  

+ **Handling Errors**  
We run terraform commands as part of bash script . Make sure to check for non-zero exitcode of each terraform command before executing next terraform command in script.

+ **Change/Pull Request Validation**  
Use below commands for review and validation  
`terraform validate`  
`terraform plan`  

+ **Masking Output**  
If for some reason you need to output secrets as part of terraform you can use sensitive attribute in output block to mask it.  
`sensitive = true`
