---
layout: default
title: Home
---

{% include header.html %}

<p class="studio-tagline">Crafting retro-inspired games, documenting the journey, and sharing what I learn along the way.</p>

---

## 🎮 Featured Games & Prototypes

* **Debut Title (In Development)** — Follow progress on the [Devlogs](/devlogs) page.
* **Playable Prototypes** — Browse released builds and experiments on the [Games](/games) page.

---

## 📝 Recent Updates

{% if site.posts.size > 0 %}
  {% for post in site.posts limit:3 %}
* **[{{ post.title }}]({{ post.url }})** *(in {{ post.categories | join: ", " | capitalize }} — {{ post.date | date: "%B %-d, %Y" }})*  
  {{ post.excerpt | strip_html | truncatewords: 22 }}
  {% endfor %}
{% else %}
* **First Entries Coming Soon** — Check back soon for [Devlogs](/devlogs) on my debut title and [General](/general) coding & pixel art tutorials.
{% endif %}

---

## 🛡️ Behind the Studio

Based in Canada, **Rurik Bruin Games** is an independent solo workshop run by **Roderick Bernard Greening**, crafting native Commodore 64 games framed in an authentic 16-color VIC-II palette.

* [Read the full story behind the studio name & R.B.G. heritage →](/about)
* [Explore our C64 hardware, Vision BASIC & SID-Wizard setup →](/tools)

---

## 🕹️ Connect & Follow

Have questions, feedback, or want to swap dev notes?

* **Code & Tools:** [GitHub (Rurik-Bruin-Games)](https://github.com/Rurik-Bruin-Games)
* **Direct Email:** [contact@rurikbruin.com](mailto:contact@rurikbruin.com)
* **Games & Demos:** [rurikbruin.itch.io](https://rurikbruin.itch.io)
{% comment %}
* **Devlog Videos:** [YouTube (@RurikBruin)](https://youtube.com)
* **Social Updates:** [Bluesky (@rurikbruin.bsky.social)](https://bsky.app) | [X (@RurikBruin)](https://x.com)
{% endcomment %}

---

<p class="studio-footer">© 2026 Rurik Bruin Games™. All rights reserved.</p>

