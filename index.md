---
layout: home
title: "Home"
---

# 👋 Welcome to My RF Journey

This blog documents my learning and projects in:
- RF & Microwave Engineering
- Antenna Design
- Satellite Communication
- Space Internet Systems

I’m reading *Fundamentals of Applied Electromagnetics* by Ulaby and preparing for my grad journey at UT Austin.

### 📚 Latest Posts
{% for post in site.posts %}
- [{{ post.title }}]({{ post.url }})
{% endfor %}
