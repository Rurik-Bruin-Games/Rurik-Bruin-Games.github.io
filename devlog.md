---
layout: default
title: Devlogs
permalink: /devlogs/
---

{% include header.html %}

# Development Logs

Behind-the-scenes updates covering game design, pixel art, mechanics, and ongoing progress on active games.

---

{% if site.categories.devlog.size > 0 %}
  {% for post in site.categories.devlog %}
### [{{ post.title }}]({{ post.url }})
*{{ post.date | date: "%B %-d, %Y" }}*

{{ post.excerpt }}

[Read full devlog →]({{ post.url }})

---
  {% endfor %}
{% else %}
*No devlogs published yet—stay tuned for the first entry!*
{% endif %}

<p class="studio-footer">© 2026 Rurik Bruin Games™. All rights reserved.</p>

