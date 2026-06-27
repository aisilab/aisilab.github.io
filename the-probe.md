---
layout: page
title: The Probe
permalink: /probe/
---

**The Probe** is the blog of the AI Safety & Interpretability Lab at the University of Southern Denmark. The posts highlight selected topics from our research and updates from the lab.

{% for post in site.posts %}
  {% include post_block.html %}
{% endfor %}
