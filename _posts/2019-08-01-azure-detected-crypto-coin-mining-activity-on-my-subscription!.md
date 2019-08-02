---
categories: Azure
tags: ['Bitcoin', 'Azure', 'Azure Security Center', 'Cryptocurrency', 'Cloud Computing']
is_post: "True"
---

### Azure detected Crypto Coin Mining activity on my subscription!
Yesterday, while exploring azure services i came across “Security Center” service. You can find this service in azure portal by -
Look for “All services” on left side of azure portal > Then search for “Security Center” and click on the “Security Center” in the result.

![Azure Security Center]({{ "/images/azsecuritycenter.png" | absolute_url }})

This takes you to the “Security Center” service blade. The thing that got my attention was the “Threat Protection” dashboard under the “Overview” tab. It showed “2 attacked resources” !!!

![Azure Attacked Resources]({{ "/images/attackedresources.png" | absolute_url }})

Seeing this i was quite curious as to what kind of attack were my resources under . And to be honest who cares to attack “MY” resources :)
To get more details, i clicked on the dashboard chart which showed “2 Attacked resources” and it opened a new “Security Alerts” blade .
AND here i got to know that the attack Azure detected was a “Crypto-mining activity” 2 days back to back.

![Azure Alerts Tab]({{ "/images/alertstab.png" | absolute_url }})

To get more details, i clicked on “Crypto-mining activity” entry and it opened the respective blade .
Here under the “Attacked resource” column, it showed the icon of the “type” of the resource and the name of resource. In my case both these resources were virtual machines with names xdn1 and xdn2 respectively.
It also showed the detection times and if the state is still active or not.

![Azure Crypto Activity]({{ "/images/cryptoactivity.png" | absolute_url }})

To get more details, i clicked on one of the entry and it went to the respective blade for that particular activity.
This blade provides high level overview of how azure detected this kind of activity and some other details.
As per the screenshot, Azure analyzed the network activity of my virtual machine and it’s logic detected that it was a crypto-currency mining activity and warns that the azure resource(VM) is compromised.

![Azure Crypto Activity Details]({{ "/images/cryptoactivity-final.png" | absolute_url }})

This alert was accurate as i was honestly doing crypto currency mining on these 2 machines as part of an experiment :) which was successful.
I wanted to continue mining :) BUT as there is a limit on the CPU cores we get in Azure Free Trial , i cannot create more VM’s to try some other azure services which require compute resources like VM’s to be created.
Hence i deleted those crypto currency mining VM’s :(
Hope people reading this blog might get some idea about……. Azure Security Center , of course :)

**NOTE:** This post was initially posted by **ME** on medium and can be found at: https://medium.com/@isunnyoswal/azure-detected-crypto-coin-mining-activity-on-my-subscription-b98b320ef30a  
