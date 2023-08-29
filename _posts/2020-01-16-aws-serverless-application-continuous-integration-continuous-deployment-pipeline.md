---
categories: [Tech, AWS]
tags: ['AWS', 'Serverless', 'Terraform', 'BitbucketPipelines','ServerlessFramework','DevOps']
is_post: "True"
is_home_btn_reqd: "True"
---
In my current project, we are using [serverless framework](https://serverless.com/) to package/deploy our serverless application on AWS.  
Deployment pipeline automates the terraform & serverless framework commands ,provision AWS infra resources and package/deploy application to AWS.

Infrastructure as Code (IaC) principle was key factor in designing this pipeline.

**Below is the flow diagram of the implementation:**  


![CI-CD-PIPELINE]({{ "/images/AWS-Serverless-CI-CD.PNG" | absolute_url }})  


Details:  
+ We use Trunk-Based Git Branching model for development.
+ Source code repository is Bitbucket and we use bitbucket pipelines as our CI/CD pipeline.
+ Above diagram was created using draw.io

Hope this helps.
