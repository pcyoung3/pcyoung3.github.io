---
title: "Synology"
layout: archive
permalink: /categories/synology
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.synology %}  
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}