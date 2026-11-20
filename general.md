---
layout: default
title: General
permalink: /general/
---

{% include header.html %}

# Articles, Tutorials & Notes

Practical coding tips, retro hardware experiments, tool breakdowns, and general thoughts on game development.

---

{% if site.categories.general.size > 0 %}
  {% for post in site.categories.general %}
### [{{ post.title }}]({{ post.url }})
*{{ post.date | date: "%B %-d, %Y" }}*

{{ post.excerpt }}

[Read full article →]({{ post.url }})

---
  {% endfor %}
{% else %}
*No articles published yet—check back soon for tutorials and dev notes!*
{% endif %}

<p class="studio-footer">© 2026 Rurik Bruin Games™. All rights reserved.</p>

