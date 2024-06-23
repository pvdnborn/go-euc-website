---
layout: post
toc: true
title:  "Comparative Quality Analysis of Citrix Codecs: H.264, H.265, and AV1"
hidden: true
authors: [ryan]
reviewers: [mick, leee, patrick, eltjo, anton, esther, ruben]
categories: [ 'Citrix' ]
tags: [ 'Citrix', 'SSIM', 'Visual quality', 'NVIDIA', 'AV1']
image: assets/images/posts/114-comparative-quality-analysis-of-citrix-codecs/comparative-quality-analysis-of-citrix-codecs-feature-image.png
---
Citrix remoting protocol, HDX, includes support for the AV1 video codec since the 2312 release. AV1 is the successor of the H.265, which itself is the successor of the widely adopted H.264 codec.This research aims to compare the visual quality differences between the three codec using Citrix HDX protocol and how that could impact the user experience.

## A dive into video codecs
For this research, it’s essential to have a basic understanding of video codecs. Video codecs enable an efficient method to deliver high quality video steams for a source to an endpoint. Codecs are found in every streaming service; YouTube, Netfix, and even your TV at home. Citrix uses the video coded technology to deliver high fidelity applications from a (private) cloud to a remote user. There are two processes with video codecs, which are encoding and decoding. Encoders are the components that take the raw media, encoding it into a more sizable package to deliver to the endpoint. At the endpoint, the encoder receives the encoded signal, which will decode where it will show the media. There are various video codecs, but the known ones are [H.264](https://en.wikipedia.org/wiki/Advanced_Video_Coding){:target="_blank"}  (released 2003), [H.265](https://en.wikipedia.org/wiki/High_Efficiency_Video_Coding){:target="_blank"}  (released 2013), and the “relatively” new [AV1](https://en.wikipedia.org/wiki/AV1){:target="_blank"}  (released 2018). These codecs have their own algorithm and hardware requirements. When encoding a digital image, there is compression applied to ensure the size of the video content. This way, you can reduce the required bandwidth to deliver the media steam. However, when using lower compression, details in the image will be preserved, resulting in a higher quality. In the case of remoting, it is vital to have a clear understanding of the workload in order to find the right balance between frame rate, bandwidth, and quality. A high frame rate with a high quality will require a higher bandwidth, so there is always a penalty.

Additionally, there is a trend with larger media. Nowadays, 4K is an accepted media size because bandwidth capacity has increased. When the internet connection is unstable, compression kicks in, and the user experiences a decrease in quality. A simple example is YouTube; when your connection is unstable, YouTube will automatically reduce the resolution from 4k to 720p when using the quality setting auto, ensuring you can continue watching.

In Citrix, the video codec is applied to deliver the desktop or application to the endpoint. However, there is additional complexity when talking about remote protocols, as there is interaction required to the remote machine. The element of interaction adds another dimension to the user experience, as you, as a user, expect this interaction to be processed within a couple of milliseconds.

Citrix has three codecs available: H.264, H.265, and AV1. For both H.265 and AV1, there are some requirements for both virtualized environments and the endpoint, as both require a GPU with a specific encoding or decoding chip. Citrix applies various methods to influence the encoder to ensure you can find the optimal configuration for your workload. You can set the Visual Quality, which will apply the amount of compression applied to the stream. Citrix has various methods to encode, such as using video codecs for compression, where multiple options exist for how the screen is encoded. For example, there is an option called “active changing regions,” which applies different encoding methods only to the region that has been updated. The rest of the screen, which is static, will be delivered using another algorithm to reduce, for example, bandwidth.

<a href="{{site.baseurl}}/assets/images/posts/114-comparative-quality-analysis-of-citrix-codecs/citrix-hdx-adaptive-display.png" data-lightbox="citrix-hdx-adaptive-display">
![citrix-hdx-adaptive-display]({{site.baseurl}}/assets/images/posts/114-comparative-quality-analysis-of-citrix-codecs/citrix-hdx-adaptive-display.png)
</a>

Source: [Experience the Elegance of Adaptive Technologies with XenApp & XenDesktop 7.13 - Citrix Blogs](https://www.citrix.com/blogs/2017/03/02/experience-the-future-of-work-now-with-xenapp-xendesktop-7-13/){:target="_blank"}

When adding a GPU, the possibility of using the GPU to handle the encoding and decoding is added. This is a more efficient method and leaves CPU resources for the applications.

Citrix invests a lot in the HDX protocol to ensure excellent quality in every scenario. However, as Citrix supports many scenarios, this also introduces complexity, as there are a lot of details you can tweak for your context.

## Methodology & setup
This research aims to assess the quality of the various encoder options in Citrix HDX. As described in the previous chapter, choosing the encoder has benefits other than quality. As the scope of this research is pure quality, it will not include any performance metrics to assess additional benefits.

To assess the quality, the structural similarity index measurement (SSIM) is used to quantify the perceived quality of digital images. This is a full reference method, which means a baseline, also known as a reference image, is required for the analysis. Additionally, Multi-Scale SSIM (MS-SSIM) is included, an advanced form of SSIM conducted over multiple scales through multiple sub-sampling stages.

Peak signal-to-noise ratio (PSNR) is an engineering term for the ratio between the maximum possible power of a signal and the power of corrupting noise that affects the fidelity of its representation. PSNR is commonly used to quantify reconstruction quality for images and videos subject to lossy compression.

As these methodologies are uncommon in the EUC context, here is a table of the scales for SSIM and PSNR.

|SSIM|PSNR|Quality|
|---|---|---|
|1|80|Excellent (same)|
|> 0.97|> 40|Good|
|> 0.95|> 30|Average|
|< 0.95|< 20|Bad|

Sources: [SSIM](https://en.wikipedia.org/wiki/Structural_similarity_index_measure){:target="_blank"}  & [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio){:target="_blank"}

A 2k 24 FPS video, [Cosmos Laundromat](https://opencontent.netflix.com/#h.uyzoa2bivz2j){:target="_blank"}  from Netflix in partnership with Blender, is used for this specific research to assess the quality of a source video. This video is selected as an open-source project from Netflix license under the [Creative Commons Attribution 4.0 International Public License](https://creativecommons.org/licenses/by/4.0/legalcode){:target="_blank"} .  The video has a nice balance between low and high-movement scenes. The more movement in the video, the more complex the encoder encodes the images. The analysis is done by analyzing individual frames from a video, which are images. Comparing these images from a video source can be difficult as the video presents 24 frames each second. Therefore, a reference QR code representing the exact frame number is added to the video, allowing us to know which frame is presented. Based on the keyframes from the baseline, the same frame can be searched in the comparison recording. A section of 770 by 600 is compared, focused on the middle of the screen containing only the video.

<a href="{{site.baseurl}}/assets/images/posts/114-comparative-quality-analysis-of-citrix-codecs/baseline-outline.png" data-lightbox="baseline-outline-crop">
![baseline-outline-crop]({{site.baseurl}}/assets/images/posts/114-comparative-quality-analysis-of-citrix-codecs/baseline-outline.png)
</a>

All recordings are done on the endpoint using OBS studio. The baseline is recorded using the same method to ensure additional compression from the recording is included in the comparison, guaranteeing a direct comparison from a local machine with the Citrix HDX sessions. Each video is recorded in lossless quality in mp4 format with H.265 for a total of 60 seconds at 120fps. The baseline video has 17 keyframes, meaning a maximum of 17 frames can be compared.

<iframe width="760" height="460" src="https://www.youtube.com/embed/Hi_Wb3G6xOA?si=BGpyDSjPcRFDSf0t" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

The video is played using Microsoft Edge and streamed from a file location on the same network.

Regarding the Citrix setup, a single VM with 4vCPU and 8GB memory runs Windows 10 Enterprise 22H2 with an NVIDIA RTX A6000 and a Q2 profile. By default, the base image is optimized using the Citrix optimizer. The VDA is the latest long-term serving release(LTSR), version 2402 CU1, where the client is the Citrix Workspace app version 24.3.1.97. The endpoint is a powerful machine containing an NVIDIA RTX 3070Ti, as a GPU is required for H.265 and AV1. Please note that the NVIDIA 30 series cannot be encoded but can decode the AV1 stream.

The following Citrix HDX policies are configured:

| Policy                                | Value                 |
| ------------------------------------- | --------------------- |
| Target frame rate                     | 60 fps                |
| Use video codec for compression       | For the entire screen |
| Use hardware encoding for video codec | Enabled               |
| Optimize for 3D graphics workload     | Enabled               |
| Graphic status indicator              | Enabled               |

The setting “use video coded for compression” which is configured on “for the entire screen” forces the protocol to always use a video coded for all the content.

The following encoders are included in this research:

- H.264, which is the default of Citrix
- H.265, which requires a supported vGPU and GPU with H.265 en/decoder on both VDI and endpoint
- AV1, which requires a supported vGPU and GPU with AV1 en/decoder on both VDI and endpoint

Additionally, each encoded type is also recorded in the following visual qualities:

- Low
- Medium
- High
- Build to lossless *
- Always lossless

It is important to understand that “build to lossless” behaves differently compared to the other qualities. Citrix dynamically adapts to various qualities based on the workload with a focus point where a user is active based on the mouse pointer. Therefore, it is not directly comparable and cannot draw any conclusions based on the result. However, it is included for reference purposes only.

## Hypothesis & results
Based on the nature of these encoders and requirements, the hypothesis is that both H.265 and AV1 provide higher quality regardless of the configuration of visual quality. Due to the compression applied to the delivered image, the quality is expected to increase from lower to always lossless.

Before comparing the various codecs, let's assess each visual quality. All data is in the 95th percentile of the matched frames. The scaling for the SSIM data is set from 0.95, marked as bad quality, to 1.0. This means the higher, the better. Please note that MS-SSIM returns higher results than SSIM due to the multi-scale algorithm used.

{% include chart.html scale='manual' scale_min='0.950' scale_max='1.0' type='hbar' data_file='assets/data/114-comparative-quality-analysis-of-citrix-codecs/h264-ssim-quality-compare.json' %}

{% include chart.html scale='manual' scale_min='0.950' scale_max='1.0' type='hbar' data_file='assets/data/114-comparative-quality-analysis-of-citrix-codecs/h265-ssim-quality-compare.json' %}

{% include chart.html scale='manual' scale_min='0.950' scale_max='1.0' type='hbar' data_file='assets/data/114-comparative-quality-analysis-of-citrix-codecs/av1-ssim-quality-compare.json' %}

For both H.264 and H.265 all SSIM data is above 0.97, which means the HDX protocol delivers a good quality compared to the baseline. It is interesting to note, that AV1 on the visual quality Low, Medium and High is below the 0.97 mark, meaning these are marked as average results. Based on this data, the visual quality is always lossless and delivers the best quality, which is as expected.

{% include chart.html scale='manual' scale_min='0' scale_max='40' type='hbar' data_file='assets/data/114-comparative-quality-analysis-of-citrix-codecs/h264-psnr-quality-compare.json' %}

{% include chart.html scale='manual' scale_min='0' scale_max='40' type='hbar' data_file='assets/data/114-comparative-quality-analysis-of-citrix-codecs/h265-psnr-quality-compare.json' %}

{% include chart.html scale='manual' scale_min='0' scale_max='40' type='hbar' data_file='assets/data/114-comparative-quality-analysis-of-citrix-codecs/av1-psnr-quality-compare.json' %}

Overall the PSNR results very similar between all codecs. All results are below the 30 which means in terms of PSNR is indicated as average.

Let's compare the different video codecs directly. In this case, the visual quality high is selected, as this is usually the recommended visual quality setting.

{% include chart.html scale='manual' scale_min='0.950' scale_max='1.0' type='hbar' data_file='assets/data/114-comparative-quality-analysis-of-citrix-codecs/codec-ssim-quality-compare.json' %}

{% include chart.html scale='manual' scale_min='0' scale_max='40' type='hbar' data_file='assets/data/114-comparative-quality-analysis-of-citrix-codecs/codec-psnr-quality-compare.json' %}

Overall, the difference is minimal and would not be noticeable for an end user. This means that the predicted quality will be the same no matter what encoder you select. Based on the data H.265 performs better on SSIM and PSNR than H.264 and AV1.

## Conclusion
Even though an end user would not care or know about video codecs they will have a big benefit as this is one of the core components of the remoting protocol. Citrix HDX has three video codecs built into its solution: H.264, H.265, and the new AV1.

Before drawing any conclusions, it is essential to note that an encoder's behavior is very workload-dependent. Comparing a task worker primarily working on a document to an architect designing buildings in a 3D CAD environment has different visual requirements. Based on the workload, it is vital to find the right balance between frames per second, visual quality, and bandwidth. Therefore, it is always important to reflect on the conclusions in your context and ensure that they are validated.

Based on this research, with the used workload, the conclusion can be made that there is a minimal difference in the perceived quality of various codecs. However, both H2.64 and H.265 are the most consisten in delivering the highest visual quality, not mather which visual quality policy configuration. The following video is a comparison of all the encoders; please note that YouTube is adding additional compression.

<iframe width="760" height="460" src="https://www.youtube.com/embed/bYdZ18LDABY?si=n2JyZuGC_owUbXSt" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

The AV1 results are a bit surprising as it was expected these would be higher compared to both H.264 and H.265. The results are consistently lower all in the range of an average experience. A user might not directly notice as the margen is close to good, but based on the data it is AV1 is not the recommended choice. Again, this conclusion is based on this research and the workload, so it might not similarly reflect your end user. As mentioned, this research's scope is the visual quality and not any other performance benefits from the various encoders. Therefore, it would be interesting to investigate the performance footprint further, as this should also be an essential factor in selecting the encoder type in your environment.

Note that a significant drawback of AV1 is the hardware requirements. NVIDIA Lovelace generation GPU or later (for example L4 / L40) or Intel Arc generation GPU or later is required on the VDI for encoding and decoding the video streams with AV1. Additionally, the endpoint requires a NVIDIA Ampere or later, Intel 11th Gen / Arc or newer, or AMD Radeon RX 6000 / Radeon Pro W6000 series (RDNA2) or later. Based on these hardware requirements it is not expected customers are going to widely adopt the AV1 video codec.

In the multi-media sector, this is already noticeable, as both Youtube and Netflix have already adopted AV1. It is excellent that there is innovation in these encoders that will ensure the best user experience. It would take time before this becomes a standard in the remote world, but time will tell.

Photo by [Santanu Kumar](https://unsplash.com/@kumar_santanu?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash){:target="_blank"}  on [Unsplash](https://unsplash.com/photos/a-close-up-of-a-persons-eye-with-the-reflection-of-the-sky-in-t_KaJGxLxvM?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash){:target="_blank"}
