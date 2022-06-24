---
layout: page
title: Esther Barthel
comments: false
---
{% assign author = site.authors['esther'] %}

<img style="float: left; width: 250px; margin-right: 30px;" src="{{ site.url }}{{ author.picture | relative_url }}" alt="{{ author.display_name }}">Esther Barthel has worked in different roles and functions as an IT consultant after she finished her Master’s degree in Computer Science in 1997. She soon discovered that End User Computing combined servers, desktops, and user experience technologies into one and specialized in virtualization solutions, like Microsoft Remote Desktop Services, Citrix Virtual Apps & Desktops and Azure Virtual Desktops

Esther works as a Solutions Architect at cognition IT, where she combines her passion for designing and implementing Citrix solutions with her newfound love for DevOps automation, using PowerShell, REST APIs and JSON payloads to automate both on-premises and Cloud Infrastructure as Code solutions.

Ever since she hosted introduction days for technical female student candidates Esther has shared her passion and knowledge for IT. She also is a strong advocate of diversity and inclusion as one of the founders of the CUGC Women in Tech mentorship program.

In addition to her Microsoft Most Valuable Professional (MVP) award, Esther is also awarded as a Citrix Technology Professional (CTP) VMware vExpert EUC, and Parallels VIPP..

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

{% include contributions.html name='esther' %}