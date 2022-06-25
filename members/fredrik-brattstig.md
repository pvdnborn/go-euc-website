---
layout: page
title: Fredrik Brattstig
comments: false
---
{% assign author = site.authors['fredrik'] %}

<img style="float: left; width: 250px; margin-right: 30px;" src="{{ site.url }}{{ author.picture | relative_url }}" alt="{{ author.display_name }}">With over 20 years of experience of the VDI/Remote Apps and Desktops niche, Fredrik lives by the mantra of providing High-End User Experience.

Fredrik enjoys greatly to examine performance metrics and User Experience of Remoting protocols in the EUC space. CAD, 3D modelling, Gaming and Virtual Reality are good ways of showing the usability of Remote Graphics.

He has a great understanding of the importance of the tech Community and doesn’t hesitate to share hints and tricks on the different technologies he gets his hands on. 
Fredrik lives in Sweden with his family of 4 kids and wife. Enjoys well cooled Lager and does his fair share of running (some might say it’s jogging)….

**Community Awards:**
| :---------------------------------- | :------------------------ |
| NVIDIA NGCA vGPU Community Advisor  | 2019, 2020, 2021 and 2022 | 
| Microsoft MVP – Enterprise Mobility | 2020-2021 |

<div class="social-button-member">
{% if author.linkedin %}
<a style="text-decoration: none;" href="{{author.linkedin}}" target="_blank"><img class="author-box-socials-icon" src="{{ site.baseurl }}/assets/images/social/027-linkedin.png" alt="linkedin"></a>
{% endif %}

{% if author.twitter %}
<a style="text-decoration: none;" href="{{author.twitter}}" target="_blank"><img class="author-box-socials-icon" src="{{ site.baseurl }}/assets/images/social/008-twitter.png" alt="twitter"></a>
{% endif %}

{% if author.web %}
<a style="text-decoration: none;" href="{{author.web}}" target="_blank"><img class="author-box-socials-icon" src="{{ site.baseurl }}/assets/images/social/030-html-5.png" alt="website"></a>
{% endif %}

{% if author.github %}
<a style="text-decoration: none;" href="{{author.github}}" target="_blank"><img class="author-box-socials-icon" src="{{ site.baseurl }}/assets/images/social/050-github.png" alt="github"></a>
{% endif %}

{% if author.reddit %}
<a style="text-decoration: none;" href="{{author.reddit}}" target="_blank"><img class="author-box-socials-icon" src="{{ site.baseurl }}/assets/images/social/018-reddit.png" alt="reddit"></a>
{% endif %}
</div>

{% include contributions.html name='fredrik' %}

