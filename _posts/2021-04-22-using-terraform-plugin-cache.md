---
categories: Azure
tags: ['Terraform', 'DevOps', 'InfrastructureAsCode']
is_post: "True"
is_home_btn_reqd: "True"
---

In my current project, we have automation scripts running terraform which have multiple providers like gcp & vault.   
+ Also as part of automation we have to cleanup the repo which has the default **.terraform** dir (where all providers were downloaded) as we cloned the git repo(very light) on every run to fetch latest repo .    
+ Due to this cleanup , everytime automation was downloading the terraform plugins/providers which would take some time depending on download speeds.

We wanted to avoid downloading same plugins/providers every time if there was no change in version.   
Then i found out about terraform **Provider Plugin Cache** . You can configure this via environment variable **TF_PLUGIN_CACHE_DIR**.

Added below 3 lines for the cache to work in the automation bash script:
```
TF_CACHE_PATH="$HOME/.terraform.d/plugin-cache"
export TF_PLUGIN_CACHE_DIR=$TF_CACHE_PATH
mkdir -p $TF_CACHE_PATH
```

Logs without cache:
```
Initializing the backend...
Initializing provider plugins...
- Finding hashicorp/google versions matching "3.51.0"...
- Finding latest version of hashicorp/vault...
- Installing hashicorp/google v3.51.0...
- Installed hashicorp/google v3.51.0 (signed by HashiCorp)
- Installing hashicorp/vault v2.19.1...
- Installed hashicorp/vault v2.19.1 (signed by HashiCorp)
```

Logs with cache hit:
```
Initializing the backend...
Initializing provider plugins...
- Finding hashicorp/google versions matching "3.51.0"...
- Finding latest version of hashicorp/vault...
- Using hashicorp/vault v2.19.1 from the shared cache directory
- Using hashicorp/google v3.51.0 from the shared cache directory
```

Official [Documentation](https://www.terraform.io/docs/cli/config/config-file.html#provider-plugin-cache)
