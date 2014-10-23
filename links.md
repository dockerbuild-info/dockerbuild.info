---
layout: page
title: Links
permalink: /links/
---

{% for post in site.links %}
#### [{{ post.title }}]({{ post.url | prepend: site.baseurl }}) - {{ post.date | date: "%Y-%m-%d" }}

{{ post.content }}
{% endfor %}
