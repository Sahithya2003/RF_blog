---
layout: splash
title: "Welcome to My RF Journey"
header:
  overlay_color: "#000"
  overlay_filter: "0.5"
  overlay_image: /assets/images/space_banner.jpg
excerpt: "RF, Space & Electromagnetics • Grad Prep Blog"
intro:
  - excerpt: "Exploring the invisible world of fields and waves."
---


# 👋 Hi, I'm Sahithya

I'm a final-year Electronics and Communication Engineering student passionate about:
- RF & Microwave Engineering
- Antenna Design & Electromagnetic Simulation
- Satellite Communication & Space Internet Systems

Currently reading *Fundamentals of Applied Electromagnetics* by Ulaby and prepping for my MS.

[Explore Blog Posts]({{ "/blog/" | relative_url }}) | [Visit Portfolio](https://sahithya2003.vercel.app/)

---

### 📷 About Me

<img src="/RF_blog/assets/images/profile_pic.jpg" width="180" style="border-radius: 50%; float: left; margin-right: 20px;"/>

I enjoy working on applied research in wireless systems and electromagnetics. My interests lie in signal propagation, antenna systems, satellite-based internet, and next-gen RF technologies.

Connect with me on [LinkedIn](https://linkedin.com/in/sahithya-kattamuri) or explore my [resume](https://sahithya2003.vercel.app/).

---

### 🧪 Blog Highlights

{% for post in site.posts limit:3 %}
- [{{ post.title }}]({{ post.url | relative_url }})
  _{{ post.date | date: "%B %d, %Y" }}_
{% endfor %}


<!-- 100% privacy-first analytics -->
<script async src="https://scripts.simpleanalyticscdn.com/latest.js"></script>
