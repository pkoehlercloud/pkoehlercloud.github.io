+++
title = "Azure VMware Solution - The fastest path to the cloud?"
date = 2024-04-10T16:56:47+06:00
draft = true
author = "Patrick Koehler"

tags = [
    "Azure",
    "Azure VMware Solution",
    "Azure Stack HCI",
    "Nutanix Cloud Clusters",
    "NC2"
]
categories = ["Azure VMware Solution", "Microsoft Azure", "VMware"]
image = "title.png"
description = "This blog series dives into setting up VMware Horizon Cloud on Microsoft Azure. In this installment, we focus on configuring a Service Principal for the VMware Universal Console. Learn how to securely grant administrative privileges, generate necessary credentials, and create a custom role with minimal permissions. Follow step-by-step instructions for seamless integration."
+++

Hi everyone it's Patrick again with the first blogpost on my new site! 

This time I want to share with you my experiences with Azure VMware Solution and if it's really the fastest path to the cloud. 
In this blog we're discovering the service from a technical perspective and will have a look at competitive products, like Nutanix Cloud Clusters (in short, NC2). 

After the completion of the aquisition of VMware by Broadcom, many customers, partners and customers are afraid of high license costs and uncertanties when it comes to product development and support in their on-premises environments. While many community folks have written blogs about this fact (some more related to bashing VMware rather than having an objective view on the technology,), this blog has the intention of... 

* Giving you a good overview of what Azure VMware Solution is and who provides the service.
* How the service incorporates with other Azure Services and which are the most commonly used in this scenario.
* What is crucial in planning Azure VMware Solution for your organisation.
* Lessons learned from the field. 

If you like this series, let me know and I'm happy to produce more architectural content around Azure VMware Solution. But for now, let's dive into the topic:

## Azure VMware Solution - Technical Overview

![alt text](image.png)

Azure VMware Solution is a first party solution of Microsoft that's been around for years already, firstly hosted by CloudSimple, nowadays hosted by Microsoft directly on Bare Metal hardware in Azure datacenters around the world. 

It consists of the well known product stack, that VMware by Broadcom is providing customers known as VMware Cloud Foundation, consisting of products like:

* VMware vSphere - Which is VMware's virtualization platform
* vSAN - VMware's storage solution
* NSX-T - Provides Network & Security virtualization capabilities
* VMware HCX - Migration & Network Extension solution provided by VMware, which is optional. 

Besides that, it's important to understand also the Azure footprint of resources which should be utilized:

* Microsoft Entra ID - IAM for administrative access for Azure based resources
* Virtual networks - Termination of VPN / Express Route - depending on the topology spoke for Azure native services.
* Site-2-Site VPN or Express Route - Used to establish connectivity between on-premises and Azure, but also to interconnect Azure VMware Platforms on Azure and interconnect with Azure services.
* Azure Virtual WAN - A networking service that bring networking, security, and routing functionalities together in a central place.  
* Azure Disk Pools or Azure NetApp Files - If datastore must be extended with high performant storage. 

While the stack that Microsoft uses to deploy AVS sounds interesting for customers that are coming from a VMware based background, ensuring that they keep the same user and administrative experience than on-premises, it might be exciting to have a quick comperative look at Nutanix Cloud Clusters (NC2), which is offering an alternative for Hybrid connectivity as well:

## Alternative: Nutanix Cloud Clusters (NC2)

![Image Source: https://learn.microsoft.com/en-us/azure/baremetal-infrastructure/workloads/nc2-on-azure/about-nc2-on-azure](image-1.png)

Nutanix launched their Cloud Clusters product (available for Microsoft Azure and AWS) last year to allow customers to benefit from  hybrid functionalities and workload mobility features than VMware, with the exception that the service is not a first party service of Microsoft Azure. 

With Nutanix cloud clusters on Azure, the deployment will consist of Bare Metal hardware (the same as Azure VMware Solution uses), with the Nutanix software stack consisting of AOS (Storage), AHV (Hypervisor), Flow (Network virtualization), Prism Central (Management GUI) and a bunch of other tehcnologies, which will be deployed Azure natively like:

![Image Source: https://learn.microsoft.com/en-us/azure/baremetal-infrastructure/workloads/nc2-on-azure/media/nc2-on-azure-deployment-architecture.png](image-2.png)

* Virtual Machines for Flow Gateways
* A NAT Gateway 
* 2 Virtual Networks (One for Bare Metal Management, the other for managing the cluster from a software perspective)

While NC2 looks like a direct competitor, I have to admit that the solution is not only more complex in terms of planning (please keep in mind that I consider the deployment and preparation of resources to be deployed as a plus of complexity compared to AVS), it also incorporates more Azure native products, which have to be well planned ahead before starting the deployment. An additional point to consider is, that NC2 won't be deployed directly from the Azure Marketplace (even though you have the chance of booking it there). You have to have a my.nutanix.com account, a valid subscription to the service and access the NC2 cloud platform via a dedicated and decentralized portal. 

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