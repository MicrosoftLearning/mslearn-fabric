---
title: Online Hosted Instructions
permalink: index.html
layout: home
---

# Microsoft Fabric exercises

The following exercises are designed to support the modules on [Microsoft Learn](https://aka.ms/learn-fabric).

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}
| Module | Lab |
| --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

