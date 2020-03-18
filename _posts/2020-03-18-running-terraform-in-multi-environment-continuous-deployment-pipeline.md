---
categories: Azure
tags: ['Terraform', 'DevOps', 'InfrastructureAsCode', 'Cloud Computing']
is_post: "True"
is_home_btn_reqd: "True"
---

Terraform is an enabler for following Infrastructure As Code (IaC) principle which lets you track , manage & provision cloud infrastructure for multiple clouds .

We decided to add terraform automation as part of our continuous delivery/deployment pipeline . While implementing this for multi environment (dev/staging/production) deployment , we learnt a lot and thought of sharing some points which will help you design/implement if you have similar use case.

+ **Version**  
Install the same terraform version on deployment pipeline which was used for testing the terraform changes locally and keep them consistent throughout all environments.  
+ **Backend**  
Use remote_state feature of terraform to keep state files stored in centralized location like AWS S3 .  
Variables  
+ **Workspace**  
Use terraform workspaces feature for multi environments like respective workspace for respective environment (dev/staging/production)
+ **Auto Approval**  
Use the auto approval switch on terraform apply if you are confident with your Pull Request review process and validations running as part of pipeline.

+ **Handling Errors**  
We run terraform commands as part of bash script . Make sure to check exitcode of each terraform command before executing next terraform command in script.

+ **Change/Pull Request Validation**  
+ **Masking Output**  
If for some reason you need to output secrets as part of terraform you can use sensitive attribute to mask it.
