---
title: Esercizi di Azure OpenAI
permalink: index.html
layout: home
---

# Esercizi di Studio AI della piattaforma Azure

Gli esercizi seguenti sono progettati a supporto dei moduli su [Microsoft Learn](https://learn.microsoft.com/training).

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}