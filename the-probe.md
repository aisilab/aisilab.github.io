---
layout: page
title: The Probe
permalink: /probe/
---

**The Probe** is the AI Safety & Interpretability Lab's blog — notes from our
research, takeaways from papers we read in our reading group, and updates from
the lab.

{% for post in site.posts %}
  {% include post_block.html %}
{% endfor %}
