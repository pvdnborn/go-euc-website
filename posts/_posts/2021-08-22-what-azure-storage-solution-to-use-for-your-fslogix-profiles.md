---
layout: post
title:  "What Azure storage solution to use for your FSlogix profiles"
hidden: false
authors: [ryan, tom, eltjo]
categories: [ 'FSLogix' ]
tags: [ 'Citrix', 'Azure', 'Storage', 'NetApp']
image: assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-feature-image.png
---
Since the acquisition of FSLogix, Microsoft has decided to include FSLogix in to the majority of the Office365 offerings. This move is resulted in a great adoption of FSLogix as it is for almost everybody available. Now of the design recommendations is to ensure the profiles are stored as close as possible. When moving to Azure cloud, it is therefore recommended to use an Azure storage services, but which one should you choose? This research will compare various Azure storage configuration to store the FSLogix profiles.

> Disclaimer: This research intends to validate the performance difference between the various storage solutions and not to reach the saturation point.

## FSLogix
FSLogix is profile container solution, designed to roam user profiles and settings between different devices and/or sessions. The FSLogix Profile Container is a more advanced solution, compared to the classic Microsoft roaming profile. A functional difference between the two is that a Profile Container persists all Windows settings and applications, regardless of where they are stored in the user profile by mounting a virtual disk to the users session at session startup and redirecting the user profile this virtual disk.
Elibility for using the FLogix profile container is included in most Office365 offerings including:

  * Microsoft 365 E3/E5
  * Microsoft 365 F1/F3
  * Microsoft 365 Business
  * Windows 10 Enterprise E3/E5 and Windows 10 Education A3/A5
  * Windows 10 VDA per user
  * Remote Desktop Services (RDS) Client Access License (CAL) and Subscriber Access License (SAL)

FSLogix solutions may be used in any public or private data center, as long as a user is properly licensed.

## Available storage solutions
There are several different storage options available for the profile containers in Windows Virtual Desktop and Citrix Cloud environments on the Microsoft Azure platform service.

The two native Azure solutions consisoft of Azure Files and the new Azure NetApp Files offering from NetApp. If a self-managed solutions is preferred, a Windows Fileserver option with Storage Spaces Direct can be considered.

More in-depth information on the different available storage solutions for FSlogix with Azure, see the official FSlogix documentation: [Storage FSLogix profile container Windows Virtual Desktop - Azure - Microsoft Docs](https://docs.microsoft.com/en-us/azure/virtual-desktop/store-fslogix-profile){:target="_blank}.

## Azure Files
Microsofts Azure Files is fully managed file share in the cloud that is accessible via the Server Message Block (SMB) protocol or Network File System (NFS) protocol. Azure file shares can be mounted concurrently by cloud and on-premises deployments.

Azure Files shares are deployed into storage accounts, which are top-level objects that represent a shared pool of storage within an Azure subscription. The Azure Files offering comes in two performance tiers, Standard and Premium.

|   | Azure Files Standard | Azure Files Premium |
|:--| :------------------- | :------------------ |
| Minimum file share size | No minimum | 100GB |
| Maximum file share size | 100 TB (with large file share feature enabled) | 100 TB (with large file share feature enabled) |
| Maximum request rate (Max IOPS) | 1000 or 100 requests per 100 ms, default | Baseline IOPS: 400 + 1 x provisioned GB * IOPS bursting: Max (4000, 3 x Baseline IOPS) * |
| Maximum ingress bandwidth | Up to 60 MiB/sec | 40 MB/s + 0.04 x provisioned GB |
| Maximum egress bandwidth | Up to 60 MiB/sec | 60 MB/s + 0.06 x provisioned GB |

*Up to a max of 100,000 IOPS

More information on the performance tiers: [Azure Files scalability and performance targets - Microsoft Docs](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-scale-targets){:target="_blank"}.

At the time of writing, Azure Files shares are available in all current Azure regions.

## Azure Netapp Files
Azure NetApp Files (ANF) is a highly available and enormously scalable platform for creating cloud-based file-share environments by NetApp. ANF is sold and supported by Microsoft and is not a 3rd party marketplace offering. 

ANF are divided into capacity pools that can consist of one or more volumes as schematically depicted below:

<a href="{{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-netapp-files-storage-hierarchy.png" data-lightbox="azure-netapp-files-storage-hierarchy">
![azure-netapp-files-storage-hierarchy]({{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-netapp-files-storage-hierarchy.png)
</a>

The maximum throughput levels for ANF is determined by the Service levels. Service levels are an attribute of a capacity pool. Service levels are defined and differentiated by the allowed maximum throughput for a volume in the capacity pool based on the quota that is assigned to the volume.

ANF comes in three service levels: Standard, Premium and Ultra. In fact, all 3 service levels are hosted on the same hardware, and the chosen tier will have pro’s and cons. For an example, if you want to host 1TiB on Azure Netap files, you can run it trough the calculator and it will show you the following:

<a href="{{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-netapp-calc-volume-1.png" data-lightbox="netapp-calc-volume-1">
![netapp-calc-volume-1]({{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-netapp-calc-volume-1.png)
</a>

As shown in the picture above, all 3 tiers will have different pricing and throughputs. Also you can change the amount of bandwith needed, for an example if you need a minimum of 200MiB/s, which tiers and size of volume you need.

<a href="{{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-netapp-calc-volume-1-throughput-200.png" data-lightbox="069-netapp-calc-volume-1-throughput-200">
![069-netapp-calc-volume-1-throughput-200]({{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-netapp-calc-volume-1-throughput-200.png)
</a>

In this case Premium is the most viable option, because it is the cheapest. The performance between the tiers are identical expect on the troughput, as this is limited per tier. The maximum of bandwith for Premium and Ultra is 4500MiB/s and Standard, according to the calculator 1600MiB/s.

For more specific limitations, see the Azure Netapp Files documentation: [Performance considerations for Azure NetApp Files - Microsoft Docs](https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-performance-considerations){:target="_blank"}

Source: [TCO ANF - NetApp](https://cloud.netapp.com/azure-netapp-files/tco){:target="_blank"}.

The service level of an existing volume can be changed dynamnically by moving the volume to another capacity pool that uses the service level you want for the volume. This in-place service-level change for the volume does not require data to me migrated and does not does not impact access to the volume.

ANF might not be available in all Azure regions yet, please consult: [Azure products by region - Microsoft Azure for more information on availability.](https://azure.microsoft.com/en-ca/global-infrastructure/services/?products=netapp){:target="_blank"}

Apart from regional availability, Microsoft requires that you have been granted access to the Azure NetApp Files service. To request access to the service, see Submit a waitlist request for accessing the service: [Submit a waitlist request for accessing the service -  Microsoft Docs](https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-register#waitlist){:target="_blank"}

More information on Azure NetApp files: [Azure File Storage Service For Enterprise Workloads - NetApp](https://cloud.netapp.com/azure-netapp-files){:target="_blank"}.

## Setup and configuration
This research has taken place in the GO-EUC lab environment in combination with Microsoft Azure. Using a site-to-site VPN the on-premises resources are made available, where multiple roles are leveraged as Active Directory and the LoadGen infrastructure. Desktops are delivered with Citrix Cloud, using a Windows 10 build 20H2 multi-session VDA which is optimized using the recommended template of the Citrix Optimizer. One single Citrix Cloud Connector is deployed in Azure just as an additional domain controller. As a single Citrix Cloud Connector more than capable of handling the amount of sessions launched during the runs in the research: [Scale and size considerations for Cloud Connectors - Citrix](https://docs.citrix.com/en-us/citrix-virtual-apps-desktops-service/install-configure/resource-location/cc-scale-and-size.html){:target="_blank"}

An on-premises LoadGen configuration is used which means the user sessions are simulated using the existing GO-EUC lab infrastructure as described [here](https://www.go-euc.com/architecture-and-hardware-setup-overview-2020/){:target="_blank"}.

Decides the normal application set used in other research, we’ve added the FSlogix agent version 2009 (2.9.7621.30127) with the following configuration: 

| Policy | Value |
| :----- | :---- | 
| Delete local profile when FSLogix Profile should apply | Enabled |
| Enabled | Enabled |
| Set Outlook cached mode on successful container attach | Enabled |
| VHD location | UNC path depending on the sceanrio |
| Virtual disk type	| VHDX |


100 users and 20 AVD machine using the SKU DS3v2 with a maximum of 5 sessions each to avoid maximizing the CPU utilization. This is based on our previous research: [What is the best Azure Virtual Machine size for WVD using Citrix Cloud? - GO-EUC](https://www.go-euc.com/what-is-the-best-azure-virtual-machine-size-for-wvd-using-citrix-cloud/){:target="_blank"}.

Performance is measured from a machine perspective, in the AVD machine.

The following scenarios are included in this research:
  * Azure Files Standard 
  * Azure Files Premium 
  * Azure NetApp File Premium

As the NetApp offerings are bandwidth limited it has been decided to test a single NetApp offering as all storage levels are delivered using the same physical hardware. In theory, this means there are no performance differences between the offerings depending on the configured size. This has been confirmed by the NetApp team which is in close collaboration with GO-EUC on this research. This can be validated using the NetApp sizing calculator which can be found [here](https://anftechteam.github.io/calc/){:target="_blank"}.

Because all Azure storage solutions have dynamic bandwidth limitations the following sizes are used in this research:

### Azure Files Standard
By default Azure Files Standard is by default limited to 60 MiB/sec for both ingress and egress, which can not be adjusted by increasing the volume size. For this scenario, a volume of 1 TiB is used. This scenario has the large file share feature not enabled.
Source: [Azure Files scalability and performance targets - Microsoft Docs](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-scale-targets){:target="_blank"}

### Azure Files Premium
Based on the ingress the default 40 MiB/sec + 0.04 * provisioned GB. A volume size of 3.2 TB is configured resulting in a 168 MiB/sec ingress and 252 MiB/sec egress. The maximum throughput for this scenario will be the lowest number, which is 168 MiB/sec.
Source: [Azure Files scalability and performance targets - Microsoft Docs](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-scale-targets){:target="_blank"}

Azure Files Premium Multichannel SMB was not available in West-Europe, so in our testing this was not active.

### Azure NetApp Files Premium
The Azure NetApp File will be configured using the medium size capacity pool of 4TiB with a volume of 3.2TiB, resulting in a throughput of 200 MiB/sec. Like mentioned before, all Netapp tiers are provided on the same hardware, which should in theory result in the same performance.

Azure NetApp files supports multi channel and SMB Multichannel is enabled by default for ANF volumes.

The default testing methodolidy is applicable for this research which is described [here](https://www.go-euc.com/insight-in-the-testing-methodology-2020/){:target="_blank"}. As the initial run contains a the FSLogix profile created this run has been excluded from all scenarios.

## Expectation and results
Based a previous research, [The impact of managing user profiles with FSLogix - GO-EUC](https://www.go-euc.com/the-impact-of-managing-user-profiles-with-fslogix/){:target="_blank"}, and initial estimation both Azure Files Premium and Azure NetApp Files should have enough throughput to facilitate the 100 FSLogix profiles. It is expected to see a higher disk queue when using Azure Files Standard due to the throughput limitation of 60 MiB/sec. This might result in higher logon times for the Azure Files Standard compared to the other scenarios.

The GO-EUC workload is CPU heavy, which in previous researches often resulted in a CPU. As mentioned in the setup and configurations section, hitting this bottleneck needs to be prevented as this can influence and skew re results. This can be confirmed by looking at the CPU utilization of each individual machine.

<a href="{{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-cpu-wvds.png" data-lightbox="host-cpu-wvds">
![host-cpu-wvds]({{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-cpu-wvds.png)
</a>
<p align="center" style="margin-top: -30px;" >
  <i>Lower is better</i>
</p>

Please note, this is the average CPU utilization of the selected runs as per the default testing methodology. This does show no CPU bottleneck has been reached for these scenarios. To make this comparison between all scenarios, an average has been taken of all the available machines.

<a href="{{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-cpu-util.png" data-lightbox="host-cpu">
![host-cpu]({{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-cpu-util.png)
</a>
<p align="center" style="margin-top: -30px;" >
  <i>Lower is better</i>
</p>


<a href="{{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-cpu-util-compare.png" data-lightbox="host-cpu-compare">
![host-cpu-compare]({{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-cpu-util-compare.png)
</a>
<p align="center" style="margin-top: -30px;" >
  <i>Lower is better</i>
</p>

There is a minimal difference between the scenario might be caused by the logical disk queueing, which will be covered later.

This research is primary focus on the various storage solution, which is mesued from the VM perspective. The logical disk metrics that are available in perfmon will include all FSLogix profiles located on the Azure storage solution. It is expected to see a similar pattern in both read and write activity.

<a href="{{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-reads.png" data-lightbox="disk-reads">
![disk-reads]({{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-reads.png)
</a>
<p align="center" style="margin-top: -30px;" >
  <i>Lower is better</i>
</p>

<a href="{{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-writes.png" data-lightbox="disk-writes">
![disk-writes]({{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-writes.png)
</a>
<p align="center" style="margin-top: -30px;" >
  <i>Lower is better</i>
</p>

<a href="{{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-disk-compare.png" data-lightbox="disk-compare">
![disk-compare]({{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-disk-compare.png)
</a>
<p align="center" style="margin-top: -30px;" >
  <i>Lower is better</i>
</p>


It is clear the peaks are affected  by the used storage solution, which is primarlly caused due the bandwith limitation. In order to confirm this the Logical Disk Queue Length should show a significant difference.

<a href="{{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-disk-queue.png" data-lightbox="disk-queue">
![disk-queue]({{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-disk-queue.png)
</a>
<p align="center" style="margin-top: -30px;" >
  <i>Lower is better</i>
</p>

<a href="{{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-disk-queue-compare.png" data-lightbox="disk-queue-compare">
![disk-queue-compare]({{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-disk-queue-compare.png)
</a>
<p align="center" style="margin-top: -30px;" >
  <i>Lower is better</i>
</p>

There are multiple factors that can influence the Logical Disk queue length. First and primary reason is the amount of IOPs the underlying storage solution can handle. Secondly this is cause by the bandwith limitation, which will result in the queueing.

Anohter important factor are the logon times, because this is the first action a user will encounter. As a multi user operating system is used it is recommended to compare the initial logon times of the first couple of users. This will show the cleanest compare between the performance of the Azure storage solutions.

<a href="{{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-logon.png" data-lightbox="logon">
![logon]({{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-logon.png)
</a>
<p align="center" style="margin-top: -30px;" >
  <i>Lower is better</i>
</p>

<a href="{{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-logon-compare.png" data-lightbox="logon-compare">
![logon-compare]({{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-logon-compare.png)
</a>
<p align="center" style="margin-top: -30px;" >
  <i>Lower is better</i>
</p>

There is a clear difference between the standard and both premium and NetApp files. As the the first 10 minutes are used, there results are not effected by any bandwith limitation and does show the true perofmrnace of the storage solution. This show both premium and NetApp are similar from a logon performance.

Finally cost is an important factor that needs to be taken into account, escapiccly in the cloud.

<a href="{{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-cost.png" data-lightbox="cost">
![cost]({{site.baseurl}}/assets/images/posts/069-what-azure-storage-solution-to-use-for-your-fslogix-profiles/069-azure-storage-fslogix-cost.png)
</a>
<p align="center" style="margin-top: -30px;" >
  <i>Lower is better</i>
</p>

It is important to mention these are cost estimation and may be different depanding on the region and other factors like bandwith and transactions. From a cost perspetvice Azure Storage standard is the most cost effiencent solution but does come with a performance penalty. When sizing a storage solution it is important to take the bandwith limitation into account, as this will have an effect on the cost. 

## Conclusion
With the transition to the cloud it is becoming a common pratice to impelemtn Fslogix as the default profile solution. There is a varitity on storage solutions avaible in Microsoft Azure and depnading on the requirements and workload. The following conclusion is based on FSLogix in a Azure AVD context and does not take any other storage workloads into account. As described in the setup and configuration, this is done using the GO-EUC workload with a total of 100 simulated users distributed over 20 AVD machines.

A conclusion can be formed from multiple perspectives. Based on the best performance, Azure NetApp files is the best option, with Azure Files Permium on a close second place. When exploratation the data on a larger sclace, it is likely NetApp will outperformn Azure Files permium. Now this is a theoretic execition and may vary due to other depencadies or limitations. This is primaly based on the disk queue length as this is the most reliable metric to mesure the performance of the storage system from a user perspective.

Based on this research taking both cost and performance in consideration, it is not recommened to use Azure File Standard for storing the FSLogix profiles in a AVD context on this size or larger.

From a cost perpective, the recommendation is to go for Azure Files Permium. Now this is depanding on the size of the comanpy, but looking at mid-sized companies, this probappply would be the best fit. Ofcourse this may vary due to other storage workloads which is not taken into account. Using the premium allows you to adjust the bandwith based on size, which provides more fexbility.

To summurize, size does matter and it is therefore very important to take the required bandwith into account. When the bandwith limit is reached it will have a tremndus impact on the overall performance and user experiance.

Photo by [Vincent Botta](https://unsplash.com/@0asa?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText){:target="_blank"} on [Unsplash](https://unsplash.com/s/photos/storage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText){:target="_blank"}
