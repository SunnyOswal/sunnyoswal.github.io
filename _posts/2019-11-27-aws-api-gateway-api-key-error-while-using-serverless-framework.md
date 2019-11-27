---
categories: Aws
tags: ['AWS', 'CloudFormation','ServerlessFramework']
is_post: "True"
is_home_btn_reqd: "True"
---
In my current project, we are using serverless framework to package/deploy our serverless application on AWS.
Our Serverless.yml consists of couple of lambda's , api gateway and related configurations.
Our deployment pipeline automates the serverless framework commands and package/deploy application to AWS.

**Issue:**  
Recently our deployment pipeline failed with error:
```
Serverless: [AWS apigateway 404 0.982s 0 retries] getApiKey({ apiKey: 'abcdefgh12', includeValue: true })
Serverless Error ---------------------------------------
ServerlessError: Invalid API Key identifier specified
```
**Troubleshooting:**

* We tried deleting the API key and the usage plan but still we got the same error on redeployment.

* Then, On navigating to  **CloudFormation > Stacks > X1234X** (OUR STACK) , we found out that same apikey ID i.e **"abcdefgh12"** which is listed in error can be seen in the "Resources" section of the stack.  

    - Then we used the **"Detect Drift"** feature which we can find in **"Stack actions"** on the same page which will initiate **"drift detection"** and the result showed the **"Drift Status"** changed to **"DRIFTED"** which we can seen in **"Stack Info"** tab. 
    - On viewing the **"Drift Results"** under **"Stack actions"**, we can get details of the resources that drifted with drift status as **"MODIFIED"**.
This convinced us that something was wrong with the stack and we decided to update the stack itself manually.

**Root Cause:**  
One of the team member, **manually deleted the api key which was created by serverless framework** and then recreated it manually using the same name. But the new apikey got a new unique Id assigned .

Therefore, whenever pipeline was trying to deploy using serverless famework the cloudformation stack will throw error as it will try to reference the API key with old ID. As the Apikey with old ID is no longer available, deployment failed.

**Fix:**  
Update cloudformation template linked with the stack (deployment) by removing the respective api key (id =**"abcdefgh12"**) , usage plan and any references from the template.

Follow below steps to achieve that:
1. On AWS console, navigate to **CloudFormation > Stacks**.
2. Find the stack **X1234X** is deployed as part of your deployment and click it to see stack details.
3. On **CloudFormation > Stacks > X1234X** and here you can click the **"Update"** button.
4. On **CloudFormation > Stacks > X1234X > Update stack** , where you need to choose **"Edit template in designer"** and then **"View in Designer"**
5. On redirected page, Look for template box which will be having JSON template of the stack. Copy the template JSON on a JSON text editor like VSCode.
6. Now find **"AWS::ApiGateway::ApiKey"** in the json template the api key(s) which are throwing error in deployment and remove them and their references from the template.
7. Copy the json template and paste it in the AWS console stack **"template box"**. Then save the template in S3 and make a note of S3 template URL.
8. Close this page and repeat the step 3 & 4 but this time choose **"Replace current template"** and paste the S3 template URL which we copied earlier and keep on clicking **"Next"** button till it comes to **"Step 4 Review"** stage.
9. Scroll to the bottom of the page, review the changes that will be done with respect to current stack in **"Change Set review"** section and if all good , scroll down to enable the checkbox that requires you to acknowledge that IAM role might be created and then Hit **"Update Stack"**.
10. This will trigger a stack deployment/update and you can wait till it finishes successfully.
11. Once it finishes, you can re-run the deployment pipeline or serverless framework command from CLI and this time it will pass :)  


**Note:** 
I know there might be other ways to troubleshoot the issue or update the cloudformation template. I tried this one and it worked. Hope it helps :)
