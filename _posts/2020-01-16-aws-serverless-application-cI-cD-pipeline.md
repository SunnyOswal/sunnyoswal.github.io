---
categories: Aws
tags: ['AWS', 'Serverless', 'Terraform', 'BitbucketPipelines','ServerlessFramework']
is_post: "True"
is_home_btn_reqd: "True"
---
In my current project, we are using [serverless framework](https://serverless.com/) to package/deploy our serverless application on AWS.  
Deployment pipeline automates the terraform & serverless framework commands ,provision AWS infra resources and package/deploy application to AWS.

**Below is the flow diagram of the implementation:**  


![CI-CD-PIPELINE]({{ "/images/AWS-Serverless-CI-CD.PNG" | absolute_url }})  

Hope this helps.
