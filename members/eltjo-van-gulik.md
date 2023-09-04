---
layout: page
title: Eltjo van Gulik
comments: false
---
{% assign author = site.authors['eltjo'] %}

<img style="float: left; width: 250px; margin-right: 30px;" src="{{ site.url }}{{ author.picture | relative_url }}" alt="{{ author.display_name }}">

Together with Ryan Ververs-Bijkerk, I co-founded the G0-EUC platform and community. I’m the Principal Product Manager for HDX & Graphics at Citrix, directing the vision and strategy for the Citrix HDX remoting protocol and I have two decades of experience in the End User Computing domain.

Recognised as a former [CTP](http://www.citrix.com/blogs/2020/02/11/welcome-to-the-2020-class-of-citrix-technology-professionals/){:target="_blank"}, my contributions span blogging, public speaking at international events like Citrix Synergy, and participating in other community-driven initiatives like TeamRGE.

Outside of work, I‘m a proud father and husband. And as a self-proclaimed beer and coffee aficionado, I prefer Russian Imperial Stouts, and the distinct tastes of Brasil/Costa Rica Arabica blends for my coffee.

In my spare time, I’m an avid Dungeons and Dragons player. Only 3rd edition though, because the newer versions suck (did I say that I was pedantic yet?).

Friends often label me as social, slightly pedantic, and suitably lazy.

Let's connect! Find me on Twitter and LinkedIn.

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

{% include contributions.html name='eltjo' %}