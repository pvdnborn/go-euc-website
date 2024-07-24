---
layout: post
toc: true
title:  "Optimizing Azure DevOps: Agents Runtime Comparison"
hidden: false
authors: [ryan]
reviewers: [eltjo, patrick, anton]
categories: [ 'Azure DevOps' ]
tags: [ 'Azure DevOps', 'ADO', 'Linux', 'Windows', 'MacOs', 'Docker']
image: assets/images/posts/117-2024-07-24-optimizing-azure-devops-agents-runtime-comparison/optimizing-azure-devops-agents-runtime-comparison-feature-image.png
---
Many organizations are adopting the DevOps way of working and using Azure DevOps services as their primary DevOps solution. With Azure DevOps pipelines, you have the option to use Microsoft-hosted agents, which run on Windows, Linux, or Mac. But what are the differences in runtime performance between these operating systems? This research delves into the runtime of pipelines on these Microsoft-hosted agents.

Background on Azure DevOps
Azure DevOps supports a collaborative culture and set of processes that bring together developers, project managers, and contributors to develop software. It allows organizations to create and improve products at a faster pace than traditional software development approaches.
While Azure DevOps is generally described in the context of traditional software development, there is a trend to adopt Azure DevOps for main IT departments, including the end-user computing (EUC) segment.
The suite consists of Azure Boards, Repos, Pipelines, Test Plans, and Artifacts. For more detailed information about these services, please visit the documentation on Microsoft's website.

[Overview of services - Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/user-guide/services?view=azure-devops){:target="_blank"}

As this research focuses on the Microsoft-hosted pipeline agents, a bit of background regarding the pipelines is necessary. Pipelines are designed to build, test, and release software through an automated process. In the context of EUC, pipelines are often used to provision infrastructure or automate processes related to environment administration.

A pipeline is defined in the [YAML format](https://github.com/GO-EUC/go-devops-research/blob/main/devops/pipelines/linux.yml){:target="_blank"} and executed on an agent. These agents are hosted by Microsoft in their cloud infrastructure, or you have the option to host the Azure DevOps agent yourself. These agents can be Windows, Linux, or Mac-based, depending on your requirements.

In the free tier, you can have five users and 1800 minutes of runtime on the Microsoft-hosted agents, along with the option to host one self-hosted agent. Parallel jobs are not included in the free tier and can be purchased for $15 a month. More information is available on [Microsoft's website](https://azure.microsoft.com/en-us/pricing/details/devops/azure-devops-services/){:target="_blank"}.

## Scope and configuration
The goal of this research is to compare the run time differences between Microsoft-hosted agents and a self-hosted agent.

This research includes the following scenarios:

  * Microsoft-hosted Windows agent, running Windows Server 2022 with Visual Studio 2022
  * Microsoft-hosted Linux agent, running Ubuntu 22.04
  * Microsoft-hosted MacOS agent, running macOS 12 Monterey*
  * Self-hosted running in Docker, running on Ubuntu 22.04

It is important to note that according to Microsoft, MacOS capacity is currently limited:

> macOS capacity is currently limited. Unlike Linux and Windows images, where our capacity is restrained by Azure's all up capacity, macOS capacity is constrained by the amount of hardware we have available.

Agents that run macOS images are provisioned on Mac Pros with a 3-core CPU, 14 GB of RAM, and 14 GB of SSD disk space. These agents always run in the US, irrespective of the location of your Azure DevOps organization.

Source: [Microsoft-hosted agents for Azure Pipelines](https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/hosted?view=azure-devops&tabs=yaml#recent-updates){:target="_blank"}

The agents are hosted on Azure general-purpose virtual machines in the selected region of your services, with a 2-core CPU, 7GB memory, and 14GB of SSD disk space. There are based on the Standard_DS2_v2 SKU, which run on the 3rd Generation Intel® Xeon® Platinum 8370C (Ice Lake), Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1GHz (Skylake) or the the Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), or the Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processors.

Source: [Microsoft-hosted agents for Azure Pipelines](https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/hosted?view=azure-devops&tabs=yaml#capabilities-and-limitations){:target="_blank"}

The self-hosted agent is running on a virtual machine in the GO-EUC lab with a AMD EPYC 7542, running on 2.9 GHz virtualized with VMware vSphere, with 2vCPU and 7GB memory, similar to the Standard_DS2_v2 SKU. The processor is a The Azure DevOps agent is hosted in a Docker container on a Ubuntu 22.04 server running on the virtual machine.

For this research, a pipeline has been created to execute Pester tests based on a PowerShell core task. Pester is a framework that can test your PowerShell code. Pester is compatible with PowerShell Core that can run on all operating systems, it is an easy method that does not have any dependencies on backend systems. The complexity of the tests can affect the overall run time of the pipeline, and the main goal is to keep these run times as low as possible. For this research, a total of 4 tests are created that will test two scripts that are in the repository. One of those tests is not the most efficient, but this is intended to see the run time between the various operating systems.

```YAML

- task: PowerShell@2
 displayName: 'Executing Pester Tests'
 inputs:
   pwsh: true
   filePath: '$(System.DefaultWorkingDirectory)/devops/pipelines/scripts/pester.ps1'
   arguments: '-SourcePath $(System.DefaultWorkingDirectory)/src/powershell -Publish -ResultsPath $(System.DefaultWorkingDirectory)/publish'

```

Each pipeline is started using the REST API, and the total execution time is calculated based on the start and end times. Each scenario has been run 10 times in total directly after each other. These were scheduled on a Sunday to ensure the overall demand was the lowest. The code used for this research can be found on [GitHub](https://github.com/GO-EUC/go-devops-research){:target="_blank"}.

## Hypothesis and results
Based on customer experience, the hypothesis is that the Linux-based agents run faster, making them a better option, however, the self-hosted will have overall better run times as there will be no queuing.

The initial results show the total time, including the queued time. When global demand is high, the pipeline is queued and will wait for an available agent.

{% include chart.html scale='auto' type='hbar' data_file='assets/data/117-2024-07-24-optimizing-azure-devops-agents-runtime-comparison/runtime-pipeline.json' %}

The data shows there is a bigger variance with the Microsoft-hosted agents, which is due to the queue time. For the self-hosted, where is no demand the total runtime is lower as this can be executed directly. Overall is the Microsoft Windows based agent the slowest based on this scenario.

When a DevOps agent is available the repository is pipeline is loaded witht he job definition and then the individual task are executed. The following chart shows total Pester test run time from the fastest run of each operating system.

{% include chart.html scale='auto' type='hbar' data_file='assets/data/117-2024-07-24-optimizing-azure-devops-agents-runtime-comparison/runtime-pester.json' %}


For the Microsoft-hosted agents, the Pester test times are very close, but the self-hosted agent running in Docker shows a higher execution time. The following table displays the times of the individual tests.


|  | MS-hosted Windows | MS-hosted Ubutnu | MS-hosted MacOS | Self-hosted Docker |
| Test 1 | 5.857 | 5.610 | 6.800 | 8.590 |
| Test 2 | 0.070 | 0.047 | 0.046 | 0.040 |
| Test 3 | 0.060 | 0.043 | 0.033 | 0.033 |
| Test 4 | 0.017 | 0.013 | 0.024 | 0.017 |
| Total | 6.004 | 5.713 | 6.903 | 8.680 |

<p style="text-align: center;">*Lower is better*</p>

The first test, which contains a loop, takes the longest and clearly has the most significant impact on the self-hosted agent running in Docker. The exact cause of the difference is unclear, but it may be related to the virtualization layer of Docker.

## Conclusion
Based on the results, it can be concluded that using self-hosted agents is overall the fastest option, as there is no queue. In terms of Microsoft-hosted agents, Ubuntu is the fastest in this context. The results show that both the overall runtime and the Pester tests are quicker, despite having the same compute configurations.

It's always important to reduce the execution time of the pipeline, and using the Linux-based agents can save a significant amount of time depending on the scenario. However, this might be different in your context, so it is recommended to validate this.

If you are constrained to a Windows-based agent due to task requirements, it is recommended to host these specific tasks in a separate pipeline stage, as stages can be targeted to a specific agent pool.
If you encounter long queuing times, you have the option to use self-hosted agents. These are dedicated to your environment only, which is the easiest way to reduce queuing time. The downside is that you have to maintain and manage these agents. Additionally, you could choose to host these agents in a Docker container. More information can be found here: [Run a self-hosted agent in Docker - Azure Pipelines](https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/docker?view=azure-devops){:target="_blank"}

Photo by [Wolfgang Weiser](https://unsplash.com/@hamburgmeinefreundin?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash){:target="_blank"} on [Unsplash](https://unsplash.com/photos/a-train-traveling-through-a-forest-filled-with-lots-of-trees-el8EOJhVjEU?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash){:target="_blank"}