+++
title = "Mastering Azure VMware Solution - The architecture"
date = 2024-04-24T11:56:47+06:00
draft = false
author = "Patrick Koehler"

tags = [
    "Azure VMware Solution",
    "Azure",
    "VMware"
]
categories = ["Microsoft Azure", "VMware"]
image = "title.png"
description = "I explore the design and architecture of Azure VMware Solution within Microsoft Azure. I delve into key components like networking, infrastructure, and additional resources, detailing essential design pillars such as Identity and Access Management, Networking, and Governance. I emphasize integrating best practices from Microsoft's Cloud Adoption Framework to ensure a structured and efficient approach to adopting AVS."
+++

Welcome to part 2 of the Mastering Azure VMware Solution series!

In this part we will have a look at the design of Azure VMware Solution and the general architecture of components deployed in Microsoft Azure. 

Disclaimer: In this article I'm mentioning and refering to a lot of valuable resources from the Cloud Adoption Framework of Microsoft. Microsoft's Cloud Adoption Framework is a collection of best practices, guidance, and tools designed to help organizations adopt cloud services with confidence and efficiency. It provides a structured approach to cloud adoption, addressing key areas such as strategy, readiness, migration, and governance.

I will focus especially on the design pillars that describe the Azure VMware Solution landing zone rather than covering the landing zone for Identity, Networking or other related services that might exist. 

## Architecture diagram

![Source: Patrick Köhler - Azure VMware Solution landing zone diagram](avs-architecture-1.png)

## The components explained

The components that you see in the landing zone concept of Azure VMware Solution are the bare minimum of resources (including some optional ones mentioned) that are relevant to... 

### Networking landing zone
* host our central networking resources
    * Resource Group - Azure Resources
    * Azure vWAN - Our central hub for all things connectivity
        * Express Route or Site-to-Site VPN connectivity from on-premises.
        * Express Route towards our Azure VMware Solution instace. 
    * Azure Firewall (optional) - Used to secure the traffic, can also be attached to the vWAN Hub. 

* allow for interconnectivity between all relevant resources
    * Resource Group - External connectivity
    * Virtual Network/s - One or multiple virtual networks which provide a direct connectivity to other resources located in the AVS landing zone. Examples: Azure NetApp Files volumes, Azure Virtual Desktops etc. These networks will be peered with the vWAN Hub.


### Azure VMware Solution landing zone
* host our infrastructure 
    * Resource Group - AVS
    * AVS - SDDC 
* get insights on our infrastructure
    * Resource Group - Operational
    * Metrics 
    * Alerts
* provide additional storage to our infrastructure for applications, as well as additional datastores.
    * Azure NetApp Files (Premium storage service)
    * Azure Disk Pools
* ensure that we can access the infrastructure securely from a desktop to manage the infrastructure in case that we don't implement on-premises connectivity yet.
    * Azure Bastion
    * Azure Virtual Desktop

### Additional resources
* to ensure we can deploy resources
    * Subscription - Which must be an Enterprise Agreement or CSP subscription for AVS. The subscription is required for each of these landing zones, if you're following the mentioned concept. However, it is not a 100% requirement. All of the mentioned resources can also live in one subscription. The consequence is that in terms of billing, a good tagging structure must be put in place.
    * Entra ID IAM - This related to Identity and Access Management ensuring that administrative users that need to manage the required resources in either landing zone have the appropriate permissions to do their job. 
    * Azure Policies - Ensure the integrity and compliance of the infrastructure by setting a baseline of policies that define how resources can be deployed, where they can be deployed etc. 
    * Network Watcher - Provides tools to monitor, diagnose, view metrics and enable or disable logs for Azure networking resources. 

## Which design pillars are relevant to an Azure VMware Solution design?

Besides the fact that we need to understand our on-premises or existing VMware infrastructure and the dependencies between workloads running in our datacenter, I recommend having a look at the following design pillars in more detail:

* <b>Identity and Access Management</b>: After deploying Azure VMware Solution, the vCenter environment includes a built-in local user named 'cloudadmin' with the CloudAdmin role. Design considerations emphasize using custom roles with role-based access control (RBAC) to implement the principle of least privilege.
    * Considerations
        * Azure VMware Solution's vCenter contains a built-in local user, 'cloudadmin', with the CloudAdmin role
        * Custom roles can be created using RBAC to ensure least privilege access.
    * Design recommendations
        * Deploy an Active Directory Domain Services (AD DS) domain controller in the identity subscription for IAM.
        * Update Active Directory Sites and Services for directing AD DS traffic to the correct domain controllers.
        * Limit assigning the CloudAdmin role to a minimal number of users.
        * Exercise caution when changing passwords for 'cloudadmin' and NSX admin.
        * Restrict Azure RBAC permissions to the specific resource group of Azure VMware Solution and its necessary users.
        * Apply vSphere permissions using custom roles at appropriate VM folder or resource pool levels, avoiding permissions at or above the datacenter level.
        * Utilize the Run command in the private cloud to add AD DS as an identity source, manage vsphere.local\CloudAdmins group, and create groups in Active Directory for RBAC management of vCenter Server and NSX-T Data Center.

* <b>Networking</b>: When establishing an Azure VMware Solution landing zone, it's crucial to design and implement networking capabilities tailored to your organization's needs. Azure offers diverse networking products and services to support various scenarios. Before deploying Azure VMware Solution, consider the following key requirements:
    * Determine whether HTTP/S or non-HTTP/S internet ingress is needed for Azure VMware Solution applications.
    * Plan the internet egress path.
    * Evaluate the need for L2 extension for migrations.
    * Assess the current use of Network Virtual Appliances (NVA) in your architecture.
    * Decide on the connectivity of Azure VMware Solution to either a standard hub virtual network or Virtual WAN hub.
    * Consider Private ExpressRoute connectivity from on-premises datacenters to Azure VMware Solution and whether to enable ExpressRoute Global Reach.
    * Define traffic inspection requirements for various scenarios:
    * Internet ingress into Azure VMware Solution applications.
    * Azure VMware Solution egress access to the internet.
    * Azure VMware Solution access to on-premises datacenters.
    * Azure VMware Solution access to Azure Virtual Network.
    * Traffic within the Azure VMware Solution private cloud.

* <b>Governance</b>: This section provides comprehensive guidance on securely implementing and governing Azure VMware Solution (AVS) throughout its lifecycle, focusing on security, governance, and compliance aspects.
    * Identity Security
        * Limit permanent access using the Contributor role and privileged account management solutions.
        * Create Microsoft Entra ID privileged access groups in Azure PIM for managing AVS clusters with time-bound access.
        * Use cloudadmin account for integrating AD DS or Microsoft Entra Domain Services with vCenter Server and NSX-T Data Center.
        * Rotate vCenter Server and NSX-T Data Center administrative account passwords regularly.
    * Environment and Network Security
        * Implement traffic filtering, OWASP rule compliance, unified firewall management, and DDoS protection.
        * Utilize VSAN Encryption with Customer Managed Keys (CMK) for encryption at rest.
        * Control vCenter Server access using dedicated privileged access workstations (PAWs).
        * Implement inbound internet request logging for guest workloads and session monitoring for outbound internet connection security.
        * Use specialized firewall and NVA services for outbound internet connectivity.
    * Backups 
        * Implement backup solutions with encryption in transit and at rest.
    * Guest Application and VM Security
        * Implement advanced threat detection e.g. using Microsoft Defender for Cloud.
        * Onboard guest VMs using Azure Arc or VMware Aria Operations for centralized management and monitoring.
        * Enable diagnostics metrics, logging, and near-real-time VM insights for workload VMs.
    * Environment governance
        * Monitor vSAN storage space, VM template storage policy, and host quota governance.
        * Implement Failure-to-tolerate (FTT) governance based on cluster size. (We will cover that later in the blog as well)
        * Monitor network traffic and configure alerts for security, planned maintenance, and Service Health.
    * Workload application and VM governance
        * Enable security posture awareness and compliance monitoring for Azure VMware Solution workload VMs.
        * Use Azure Arc enabled servers for VM management and Azure native resource tooling.
        * Implement workload VM logging, monitoring, update governance, backup governance, and DR governance.
    * Compliance 
        * Monitor compliance using Microsoft Defender for Cloud.
        * Ensure DR configuration compliance and backup compliance for workload VMs.
        * Adhere to country/region-specific or industry-specific compliance requirements.
        * Implement firewall audit reporting on endpoints for regulatory compliance.


## Which resources can I leverage for more information?
In terms of the mentioned design pillars you can get more information from the following resources that I linked you down below:

* https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/scenarios/azure-vmware/
* https://www.youtube.com/watch?v=BGw5Nv_Kpiw
* https://github.com/ravi0130/azure-avs-microhack

## Conclusion

And thats it! I hope this article helped you to get a better understanding of the AVS architecture. In my next blog we will start deploying the service and let the magic happen!
