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

Based in Canada, this independent workshop is run by solo developer **Roderick Bernard Greening**. Every part of the studio's identity ties back to personal heritage and the craft of game making:

* **Rurik (*Roderick*):** Drawn from the Old Norse *Hrœrekr* and East Slavic *Rurik*—the historical roots of *Roderick*, meaning *"famous ruler."*
* **Bruin (*Bernard*):** Inspired by the Old Germanic *Berinhard* ("brave as a bear") and the classic folkloric name for a brown bear—representing steady endurance and craftsmanship.
* **The R.B.G. & C64 Connection:** The initials **R.B.G.** bridge **R**oderick **B**ernard **G**reening with the **R**ed, **B**lue, and **G**reen pixels of classic computing—framed in an authentic 16-color Commodore 64 VIC-II palette honoring the 8-bit roots that inspired the studio.

---

## 🕹️ Connect & Follow

Have questions, feedback, or want to swap dev notes?

* **Code & Tools:** [GitHub (Rurik-Bruin-Games)](https://github.com/Rurik-Bruin-Games)
* **Direct Email:** [contact@rurikbruin.com](mailto:contact@rurikbruin.com)
* **Playable Builds:** [itch.io](https://rurikbruin.itch.io)
{% comment %}
* **Devlog Videos:** [YouTube](https://youtube.com)
* **Updates:** [Bluesky](https://bsky.app) | [X](https://x.com)
{% endcomment %}

---

<p class="studio-footer">© 2026 Rurik Bruin Games™. All rights reserved.</p>

