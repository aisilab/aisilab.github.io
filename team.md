---
layout: page
title: Team
permalink: /team/
---

*The AI Safety & Interpretability Lab is currently being established. This page will be updated as soon as new members join.*

{% assign sorted_team = site.team | sort: "order" %}
{% for member in sorted_team %}
## {{ member.name }}

*{{ member.role }}{% if member.since %}, since {{ member.since }}{% endif %}*

{{ member.content }}

{% if member.email or member.github or member.scholar or member.linkedin %}
{% if member.email %}[Email](mailto:{{ member.email }}){% endif %}
{% if member.github %} | [GitHub]({{ member.github }}){% endif %}
{% if member.scholar %} | [Google Scholar]({{ member.scholar }}){% endif %}
{% if member.linkedin %} | [LinkedIn]({{ member.linkedin }}){% endif %}
{% endif %}

{% endfor %}
