+++
title = "Azure VMware Solution - The fastest path to the cloud"
image = "title.png"
date = 2024-04-22T11:00:47+06:00
draft = true
author = "Patrick Koehler"
tags = ["Azure","Azure VMware Solution","AVS","Nutanix NC2"]
categories = ["Azure VMware Solution", "Microsoft Azure"]
description = "In my latest blog post, I delve into Azure VMware Solution as a potential fast track to the cloud, comparing it with Nutanix Cloud Clusters. I explore the technical details of both solutions, emphasizing Azure VMware Solutions seamless integration with Azure services and pointing out the complexity of planning and deploying Nutanix. This is part 1 of the exciting journey towards your VMware Multi Cloud story."
+++

Hi everyone it's Patrick again with the first blogpost on my new site! 

This time I want to share with you my experiences with Azure VMware Solution and if it's really the fastest path to the cloud. 
In this blog series we're discovering the service from a technical perspective and will have a look at competitive products, like Nutanix Cloud Clusters (in short, NC2). 

After the completion of the aquisition of VMware by Broadcom, many customers, partners and customers are afraid of high license costs and uncertanties when it comes to product development and support in their on-premises environments. While many community folks have written blogs about this fact (some more related to bashing VMware rather than having an objective view on the technology,), this blog series has the intention of... 

* Giving you a good overview of what Azure VMware Solution is and who provides the service.
* How the service incorporates with other Azure Services and which are the most commonly used in this scenario.
* What is crucial in planning Azure VMware Solution for your organisation.
* Lessons learned from the field. 
* Providing a big comparision of features, scalability, security and cost for your decision making process!

If you like this series, let me know and I will be happy to dive deeper in one or the other topic as demanded. But let's start with part 1 - the technical overview of Azure VMware Solution and its competitor Nutanix NC2. 

## Azure VMware Solution - Technical Overview

![Slide from Patrick Köhler - Datacentre evacuation using AVS - presented at VMUG 2023](image.png)

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

### First summary

While NC2 looks like a direct competitor, I have to admit that the solution is not only more complex in terms of planning (please keep in mind that I consider the deployment and preparation of resources to be deployed as a plus of complexity compared to AVS), it also incorporates more Azure native products, which have to be well planned ahead before starting the deployment. An additional point to consider is, that NC2 won't be deployed directly from the Azure Marketplace (even though you have the chance of booking it there). You have to have a my.nutanix.com account, a valid subscription to the service and access the NC2 cloud platform via a dedicated and decentralized portal. 

### How we continue to explore AVS

As this article provided you with a very good first touchpoint of both solutions, we will focus again on Azure VMware Solution as our core product. In the next blog article, I'm going to walk you through the process of deploying an AVS datacenter and the preparation tasks you have to consider from a design perspective. 