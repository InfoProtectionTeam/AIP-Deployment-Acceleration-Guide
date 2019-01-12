![](/Media/Title.png) 

---

> MICROSOFT MAKES NO WARRANTIES, EXPRESS OR IMPLIED, IN THIS DOCUMENT. 
>
> Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation. 
> Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, our provision of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property. 
>
> The descriptions of other companies’ products in this document, if any, are provided only as a convenience to you. Any such references should not be considered an endorsement or support by Microsoft. Microsoft cannot guarantee their accuracy, and the products may change over time. Also, the descriptions are intended as brief highlights to aid understanding, rather than as thorough coverage. For authoritative descriptions of these products, please consult their respective manufacturers.
>
> © 2019 Microsoft Corporation. All rights reserved. Any use or distribution of these materials without express authorization of Microsoft Corp. is strictly prohibited. Microsoft and Windows are either registered trademarks or trademarks of Microsoft Corporation in the United States and/or other countries. 
>
> The names of actual companies and products mentioned herein may be the trademarks of their respective owners.

---
[TOC]
# Azure Information Protection - Deployment Acceleration Guide

## Overview

Azure Information Protection (AIP) is a cloud-based solution that can help organizations to protect sensitive information by classifying and (optionally) encrypting documents and emails on Windows, Mac, and Mobile devices. This is done using an organization defined classification taxonomy made up of labels and sub-labels.  These labels may be applied manually by users, or automatically by administrators via defined rules and conditions.  Additionally, recommendations may be provided to guide users in making appropriate labeling decisions.

The Azure Information Protection Get to Production (GTP) team is part of the Azure Security Engineering Division at Microsoft. Our team in comprised of subject matter experts who work closely with select customers (usually based on size and/or complexity) to help them realize the value of their investment by providing guidance for **successful** **and** **rapid deployments**. 

This document is intended for Information Security, IT Security, and Compliance managers interested in understanding our recommendations for approaching an Information Protection project. While there will be some technical discussion throughout various sections of this document, we have found that the most challenging part of an AIP deployment is the business process and decisions that must be made. To that end, this document will focus less on how to technically implement AIP and more on a discussion of why you are doing each step. The technical details have documented in various locations, so we have provided
links to these throughout the document and aggregated them in the [appendix](#appendix-additional-resources).

To successfully implement AIP as a solution, we recommended following the phases of the Microsoft Information Protection Lifecycle as shown below:

![](/Media/Phases.png)

The lifecycle of a specific document will be comprised of the following phases: Discover, Classify, Protect, and Monitor. During each of these steps, the document will become incrementally better managed and more secure. 

Interestingly, a deployment of this technology will also follow these four phases, since experience shows it is best to introduce each of these capabilities in orderly succession. At each step, your information becomes better managed and more secure, and you don't need to wait until you are ready to go all the way to begin realizing value from your investments.

In this document, we will review each of these phases and provide best practices and lessons learned from deployments that we, the AIP GTP team, have assisted with across many different companies and industries. 

## The Microsoft Information Protection Story

Azure Information Protection (as described above) is one part of the larger [Microsoft Information Protection](https://www.microsoft.com/en-us/security/technology/information-protection) story.  With Microsoft Information Protection, Microsoft is streamlining how all applications that require information protection capabilities interact.  This provides a unified interface where both Microsoft and Partner applications can classify and protect sensitive information. Ultimately, this mean better integration across our information protection solutions and a more consistent approach to protecting your sensitive data.

The image below shows technologies that currently interface with Microsoft Information Protection. Although we will not delve deeply into all these technologies in this document, we will provide resources in the [appendix](#appendix-additional-resources) that may be used for further exposition.  

![](/Media/MIP.png)

### Microsoft Information Protection SDK

The [Microsoft Information Protection (MIP) SDK](https://docs.microsoft.com/en-us/information-protection/develop/overview) uses Sensitivity Labels from the Security and Compliance Center, so any integrations based on the MIP SDK will display labels from this source.  Activation (migration) of sensitivity labels to the Security and Compliance Center is currently in Public Preview and this process is in our [documentation](https://docs.microsoft.com/en-us/azure/information-protection/configure-policy-migrate-labels). We recommend starting with creation of labels in the Azure Portal and Activating them when this process is in General Availability.  

### Microsoft 365 Information Protection Roadmap

The [Microsoft 365 Roadmap website](https://www.microsoft.com/en-us/microsoft-365/roadmap) has public details about when new features are in development and when they have been launched. To see details around new AIP features in development, rolling out, and launched, search for [Information Protection](https://www.microsoft.com/en-us/microsoft-365/roadmap?filters=&searchterms=information%2Cprotection) and check the boxes to select which details you are interested in.

![](/Media/roadmap.png)

## Getting Started

Before jumping into the various sections detailing the stages of AIP deployment, there are some planning and prerequisite items that should be addressed.  These include general prerequisites, scheduling and recommended attendance of meetings, and support and communication planning.  In this section, we will touch on these items and provide some general guidance based on what we have seen help customers be successful in deployments of AIP.

While these tips are meant to help guide you on your AIP deployment journey, please keep in mind that these items will vary widely depending on the size and complexity of your organization. 

### General Requirements

The full list of AIP requirements can be found on our [official documentation site](https://docs.microsoft.com/en-us/azure/information-protection/requirements).  We have summarized some of the more important ones that are required for all deployments of AIP. If you have complex security rules or require offline client access, we highly advise reading the full documentation before deploying.

**Licensing Requirements**

In this document, we will describe features of Azure Information Protection that are only available through an [Azure Information Protection Plan](https://azure.microsoft.com/en-us/pricing/details/information-protection/). This is most commonly bundled with Enterprise Mobility and Security, Microsoft 365, or Identity and Information Protection licensing SKUs, but AIP can also be purchased as standalone licenses. [Office 365 plans](https://download.microsoft.com/download/E/C/F/ECF42E71-4EC0-48FF-AA00-577AC14D5B5C/Azure_Information_Protection_licensing_datasheet_EN-US.pdf) include AIP for protection only, but this document will assume an AIP licensing plan that covers the full feature set.

**Azure Active Directory**

Your organization must have an Azure Active Directory (Azure AD) to support user authentication and authorization for Azure Information Protection. Configuring Single Sign-On (SSO) for Azure AD is also highly recommended for a seamless user experience.  Additional details on requirements for Azure AD can be found in [our documentation](https://docs.microsoft.com/en-us/azure/information-protection/requirements-azure-ad).

**Network Connectivity**

A common stumbling block for many AIP deployments are the [Firewall and Network Requirements](https://docs.microsoft.com/en-us/azure/information-protection/requirements#firewalls-and-network-infrastructure).  Azure Information Protection uses a subset of the [Office 365 URLs and IP address ranges](https://docs.microsoft.com/en-us/office365/enterprise/urls-and-ip-address-ranges).  We recommend following the guidance in the Office 365 documentation and opening all **Required** URLs and IP addresses listed under the [Microsoft 365 Common and Office Online](https://docs.microsoft.com/en-us/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) section. Additionally, if using Office 2010 there is an optional URL required.

One additional callout is that termination of TLS client-to-service connections (for example, to do packet-level inspection) to the aadrm.com URL will break certificate pinning required by AIP to ensure secure communications with the Microsoft Root Certificate Authority.

### Project Planning

Project planning is an art unto itself and when done well it can set you up for an efficient deployment experience.  Although we will not delve into the full project planning that goes into deploying AIP, below are some initial meetings that we typically have with our customers and stakeholders that we recommend attend those meetings.

**Kick Off Meeting**

This can be a single long meeting, or a series of more targeted meetings followed by a combined discussion that will help to define a common vision for what the organization needs from AIP and how best to achieve that goal. Common attendees for these meetings are the CISO, Cloud Architect, Project Manager, Business Unit Decision Makers, and Technical Teams.

Some of the items that are commonly discussed at this meeting or meetings are:

- Identification of Executive Sponsor and Project Manager
- Development of Business Scenarios and Use Cases
- Decisions around high level Taxonomy (to use existing classifications or start fresh)

**Milestones and Success Criteria Meeting**

This meeting will be to discuss the major milestones that will ensure that the project is moving at an acceptable pace and define the success criteria for the entire deployment.  This meeting should take place before production rollout begins but may be held after production POC and feedback cycles to help with defining realistic timeframes for deployment. Common attendees for these meetings include CISO or representative, Project Manager, Technical Teams, Support and Continuous Engineering Teams.

Some of the items that are commonly discussed at this meeting are:

- Deployment timelines and Milestones
- Rollout Plan
- End-user Education
- Technical Support Requirements

## Discover

Implementing an Information Protection solution should not be a one-time event, but rather a journey with many steps along the way.  That said, it is important to simplify this journey and address implementation and rollout using a phased approach.

The first phase which every company can and should do to accelerate the deployment discussion and have more visibility into their data is the Discover phase.

The Discover phase will provide information into the kind of data that resides in different repositories within your organization.  AIP can identify and visualize sensitive information within documents, which can help to drive appropriate levels of attention and urgency behind deployment of information protection technologies. 

Before we can begin discussing the right methods for performing discovery, we must understand where data is stored within an organization.  Microsoft and our deployment partners have identified four primary scenarios that describe the location of customer data.

1. On-Premises Data Only – The organization has not yet begun migrating (or has migrated very little) data to the cloud.
2. Beginning Data Migration – The organization has begun migrating data, but most of the data is still residing on-premises.
3. Hybrid Data Management – The organization has moved a large amount of data to the cloud but retains some data on-premises.
4. Full Cloud Data Management – The organization has moved all important data to the cloud and has mandated cloud storage to be the primary data repository for new data. 

Now that we have discussed where data may be stored, we can talk about the supported options for discovery for these organizations.  

- For organizations that have not yet moved their data to the cloud (organizations that fall under #1 above), we recommend deploying the [AIP Scanner](https://docs.microsoft.com/en-us/azure/information-protection/deploy-aip-scanner) to do discovery against their on-premises repositories (CIFS/SMB file shares and SharePoint libraries). 
- For organizations doing full cloud data management (#4 above), we recommend using [Microsoft Cloud Application Security](https://docs.microsoft.com/en-us/cloud-app-security/what-is-cloud-app-security) (MCAS) and [Office 365 DLP](https://docs.microsoft.com/en-us/office365/securitycompliance/data-loss-prevention-policies) capabilities to do discovery of sensitive information residing in various web repositories.
- For organizations that have data in both locations (#2 & 3 above), a combination of both methods will be necessary to perform comprehensive discovery of sensitive information.  This represents most of the organizations we typically work with.

The rest of this section will cover the capabilities of the AIP scanner and a brief overview of MCAS and Office 365 DLP capabilities.

### Azure Information Protection Scanner 

One of the most daunting tasks facing organizations today is the large amount of architectural debt they have accrued over decades of operation.  This includes a vast amount of data in on-premises repositories around the world that is unclassified and often improperly secured. Gaining visibility into this data is a challenge that every organization must face.

The Azure Information Protection scanner is the solution that Microsoft uses internally for this challenge and recommends to our customers. The AIP scanner runs as a service on Windows Server and is efficient for bulk operations on data stores like CIFS/SMB file shares and SharePoint On-Premises libraries. Using the AIP scanner, you can generate a report listing all the scanned files and any sensitive information types discovered in those documents.

Note: The AIP scanner does not change the last modified date or any content within documents when running Discovery.

The AIP scanner uses the same content matching engine that Office DLP uses, and comes with more than [80 out-of-the-box information types](https://docs.microsoft.com/en-us/office365/securitycompliance/what-the-sensitive-information-types-look-for) which the AIP scanner will match against with no specific configuration from the customer side. This means that the AIP scanner can be used for discovery even before defining a classification taxonomy.

[Setting up the AIP Scanner in Discovery mode](https://techcommunity.microsoft.com/t5/Azure-Information-Protection/Cataloging-your-Sensitive-Data-with-AIP-Even-Before-Configuring/ba-p/267241) using the built-in information types is a fairly simple process, but we always recommend testing on non-production repositories before deploying to production. The AIP scanner can also do matching by using custom Regex patterns or plain text phrases. 

After you have configured and run the AIP scanner in discovery mode you can review the Data discovery dashboard which allows you can drill down in specific repositories to the file level. In the image below, some files had been manually labeled as seen on the left but millions of files with sensitive information were located as seen on the right.  If you have not implemented AIP in your environment, you will not see any data on the left (this is expected).

![](/Media/DataDiscovery.png)

Because the AIP scanner works best when on a local network with the data it is scanning, most organizations will need multiple AIP scanner instances to cover all of their on-premises repositories.  We have the new Scanner \ Nodes section in the Azure portal that provides information on the status of multiple AIP scanner instances.

![](/Media/Nodes.png)

### Microsoft Cloud Application Security & Office 365 DLP

As most customers are currently in a hybrid state for their data management, it is also prudent to discuss the options for discovering data with Microsoft Cloud Application Security and Office 365 DLP.  Both solutions can be used for discovery and we use a combination of these at Microsoft to secure our data.

Microsoft Cloud Application Security can be used to perform Cloud Discovery of sensitive information in the cloud by using your traffic logs to dynamically discover and analyze the cloud apps and sensitive information that your organization is using. 

For more information about getting started with MCAS, see [Deploy Microsoft Cloud App Security](https://docs.microsoft.com/en-us/cloud-app-security/getting-started-with-cloud-app-security).

For more information about integrating MCAS and AIP, see [MCAS and AIP integration](https://docs.microsoft.com/en-us/cloud-app-security/azip-integration).

Office 365 DLP is included in Office 365 E3 and E5 plans and provides the capability to discover sensitive information in Office 365 services such as Exchange Online, SharePoint Online and OneDrive for Business.

For more information on Office 365 DLP policies, please see [Overview of O365 Data Loss Prevention policies.](https://docs.microsoft.com/en-us/office365/securitycompliance/data-loss-prevention-policies)