---
layout: post
categories: [Tech, Azure]
tags: ['Terraform', 'DevOps', 'InfrastructureAsCode', 'Azure']
image:
  path: /images/az.png
  alt: azure
---

As per best practices of Terraform, state file should be stored in a remote backend storage like azure blob storage , aws S3 , etc and there should be a lock mechanism on this state file which prevents concurrent state operations, which can cause corruption.

For one of the project i am working for, we decided to use azure blob storage for terraform state file remote backend storage as it has inbuilt lock mechanism. 

We also have a separate IaC repo for which we have a CI/CD pipeline which does terraform validations and apply them.
Below is the flow diagram of the implementation:

![CI-CD-PIPELINE]({{ "/images/Terraform-workflow.gif" | absolute_url }})

But sometimes, if you either cancel pipeline in between a run or there are multiple pipelines running at the same time you get below error :

```
Error: Error locking state: Error acquiring the state lock: state blob is already locked
Lock Info:
  ID:        10101010-1010-1010-1010-101010101010
  Path:      myAzureStorageAccountContainerName/terraform.tfstate
  Operation: OperationTypePlan
  Who:       XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
  Version:   0.13.3
  Created:   2020-12-13 05:34:41.79534 +0000 UTC
  Info:      


Terraform acquires a state lock to protect the state from being written
by multiple users at the same time. Please resolve the issue above and try
again. For most commands, you can disable locking with the "-lock=false"
flag, but this is not recommended.
```


**FIX can be done via UI or AZ CLI or TF CLI**:    
**UI :**
+ On Azure portal , browse to the blob (terraform.tfstate) file and you will see that the blob has "Leased" state .
+ Click "Break Lease" as shown in screenshot below:   
![BREAK-LEASE]({{ "/images/AzBlobLeaseBreak.jpeg" | absolute_url }})

**AZ CLI :**
+ Get your azure storage account name , container name and storage account access key which is configured to be used as remote backend in terraform and update command used in next step.
+ Run below command which uses azure cli:  
```
az storage blob lease break -b terraform.tfstate -c myAzureStorageAccountContainerName --account-name "myAzureStorageAccountName" --account-key "myAzureStorageAccountAccessKey"
```
Blob file (Terraform.tfstate) lock will be removed successfully if you get 0 as output of the above command.

**TF CLI :**
+ Get LockId from error .
+ Run below command which uses terraform cli:  
```
terraform force-unlock <LockId>
```
Now you can retry the pipeline and it will run as expected without above error.
