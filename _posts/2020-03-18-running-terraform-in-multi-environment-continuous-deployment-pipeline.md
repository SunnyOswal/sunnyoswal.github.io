---
categories: Azure
tags: ['Terraform', 'DevOps', 'InfrastructureAsCode', 'Cloud Computing']
is_post: "True"
is_home_btn_reqd: "True"
---

Terraform is an enabler for following Infrastructure As Code (IaC) principle which lets you track , manage & provision cloud infrastructure for multiple clouds .

We decided to add terraform automation as part of our continuous delivery/deployment pipeline . While implementing this for multi environment (dev/staging/production) deployment , we learnt a lot and thought of sharing some points which will help you design/implement if you have similar use case.

Version
Backend
Variables
Workspace
AutoApproval
Handling Errors
Change/Pull Request Validation
