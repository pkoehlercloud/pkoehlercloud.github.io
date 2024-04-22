## Comparison 

Now we want to take a closer look at both solutions and try to compare them from a technical perspective. 
I will make this comparision based on the following values:

* Time to Production
    * Deployment complexity
    * Deployment speed 
* Reliability & Availability 
    * Regional Availability
    * Hardware SKU's
    * High Availability 
* Scalability
    * Horizontal Scaling (Increase hardware ressources, time to spin up)
    * Auto scaling possibilities
* Interoperability and Compatibility
    * Feature Set
    * Automation capabilities (Initial deployment up to Day 2 operations)
* Security
    * Data encryption
    * Data in transit 
    * Identity and Access Management (Azure native and in-platform)
* Documentation and Support
* Costs to consider (Last update: May 2024)

### Time To Production

#### Deployment complexity ####
Having a look at the deployment complixity it's crucial to understand the circumstances and environmental variables that I used to perform the ramp up. The following subchapters define the complexity based on metrics like:

##### Prerequisites #####
To get started with Azure VMware Solution we need to have an appropriate subscription type in order to get started. 
Currently, Microsoft supports only AVS deployments with the use of the following subscription types:
* Microsoft Enterprise Agreement (EA)
* Cloud Solution Provider (CSP)
* Microsoft Customer Agreement (MCA)

More information and reference can be found here: https://learn.microsoft.com/en-us/azure/azure-vmware/plan-private-cloud-deployment

In addition, we need to request the host quota for our deployment, which requires a Microsoft support ticket from the Azure portal. The case have been closed within 2 business days (up to five business days are noted in the Microsoft reference). 

Afterwards we need to ensure, that our resource provider in our subscription is registered (the whole process took 5 minutes to complete).

Besides that AVS only requires a /22 CIDR as bare minimum for the deployment to get started. Pretty easy and all steps could be resolved within 2 business days (including support ticket waiting time). 

For Nutanix NC2 we had to ensure the following tasks. While Microsoft requires certain subscription types to work with Azure VMware Solution, Nutanix offers a broader way of booking the service which isn't limited to any subscription type. However the booking is a bit more complex as you require a my.nutanix.com account and need to sign up for the cloud portal at Nutanix first, before you can actually purchase the service and start the deployment itself. 

Like AVS, Nutanix requires also to have the Microsoft support team involved to ensure that the quota and required nodes are made available to your subscription and the desired region. This process is off our influence and took also two business days to complete. 

As we will deploy a few more resources on Azure, Nutanix created a custom role to fullfil the zero trust principle and by leveraging the least permission principle. As this is not required for AVS I can't put it into comparision. The custom role creation and assignment to an Enterprise Application for the deployment took 15 minutes to complete. 

Next is to register the Resource Provider for Nutanix on Azure. This took 5 minutes to complete. 

As for Nutanix we need to plan our network a bit more, as the deployment wizard requires us to define three networks in total, one for the BareMetal instance NICs, one for the Management of resources via Prism Central and Flow Gateways, and one for connecitity. 




#### Deployment speed ####
In this test I performed a deployment for both Azure VMware Solution as well as Nutanix Cloud Clusters on Azure within the same region. As a baseline it is important to have the same node count, that means that we have to take the minimum production ready node count for both solutions, which is 3 nodes (based on the bare minimum of Azure VMware Solution). Whereas Nutanix Cloud Clusters support Single-Node deployments it is important that even Nutanix says that those are meant for testing and PoC's, rather than production environments. 

In addition, we have chosen West Europe as our region for the deployment, to have a direct comparision. 

##### Result #####
Both deployments of three node clusters have been accomplished in around four hours from the initial deployment kickoff. Nutanix Cloud Clusters completed a bit earlier, which relates to <b>3 hours and 56 minutes</b> for the completion, whereas <b>Azure VMware Solution took 4 hours and 13 minutes</b> to complete. 

This makes <b> Nutanix </b> winner for at least the initial deployment based on the timely value. 