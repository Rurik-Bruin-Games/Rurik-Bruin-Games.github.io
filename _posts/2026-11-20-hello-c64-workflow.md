---
layout: post
title: "Hello, C64: Building Games the Native Way"
date: 2026-11-20
categories: devlog
---

Welcome to the first devlog for **Rurik Bruin Games**.

When we set out to make new games for the Commodore 64, we wanted the development process to feel just as authentic as playing on an original cartridge or disk. Instead of relying on modern cross-compilers, external pipelines, or Windows-only toolchains, we decided to keep our game creation **native to the C64 itself**. "You might think I'm crazy..." (Ric Ocasek/The Cars)

### Why Native Development?

Whether we are writing code on our **Commodore 64** or testing inside **VICE**, keeping everything inside the C64 environment creates a tight, distraction-free feedback loop:

* **Code & Engine:** We use **[Vision BASIC](https://visionbasic.net)** to write structured, readable code that compiles directly into fast 6510 machine code—complete with inline assembly when we need raw speed.
* **Sprites & Characters:** Rather than bouncing between modern pixel editors and conversion scripts, we build our sprites and custom character sets directly on the C64 using Vision BASIC's built-in editors (like **The Spreditor**).
* **Music & SFX:** Our 3-channel SID soundtracks are composed natively inside **[SID-Wizard](https://csdb.dk)**, letting us hear every filter sweep and pulse-width modulation straight from the source.

### Where Modern Tech Fits In

Our **Pop!_OS** Linux workstation stays strictly in a supporting role—hosting VICE for quick iteration and running Git to push these Markdown devlogs straight to GitHub Pages.

If you're curious about the exact hardware, software, and links behind our studio setup, check out our full **[Tools & Setup](/tools/)** page.

### What's Next?

Now that our workbench is ready, and we have our studio web site setup, we can get down to brass tacks and start some actual coding. Over the coming weeks and months, We will be posting devlogs, as well as articles discussing different tools, techniques, etc. So stay tuned.

