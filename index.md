---
title: Microsoft Fabric interactive exercises
permalink: index.html
layout: home
---

# Microsoft Fabric interactive exercises

[Microsoft Fabric](https://learn.microsoft.com/fabric) is a unified analytics platform that brings together data engineering, data warehousing, real-time intelligence, data science, and business intelligence in one integrated software as a service (SaaS) experience.

These interactive exercises give you practical experience with Fabric's core capabilities so you can build confidence and prepare for real-world projects and certification exams.

<style>
  .tab-toggle { display: none; }
  .tab-label { display: inline-block; padding: 8px 20px; cursor: pointer; font-weight: bold; border: 1px solid #ccc; border-bottom: none; border-radius: 6px 6px 0 0; background: #f0f0f0; margin-right: 4px; }
  .tab-toggle:checked + .tab-label { background: #fff; border-bottom: 1px solid #fff; margin-bottom: -1px; position: relative; z-index: 1; }
  .tab-panels { border-top: 1px solid #ccc; padding-top: 16px; }
  .tab-panel { display: none; }
  #tab-topic:checked ~ .tab-panels .panel-topic { display: block; }
  #tab-course:checked ~ .tab-panels .panel-course { display: block; }
</style>

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}
{% assign defined_categories = site.data.lab-metadata.categories %}
{% assign defined_courses = site.data.lab-metadata.courses %}

<input type="radio" name="tabs" id="tab-topic" class="tab-toggle" checked>
<label for="tab-topic" class="tab-label">By topic</label>
<input type="radio" name="tabs" id="tab-course" class="tab-toggle">
<label for="tab-course" class="tab-label">By course</label>

<div class="tab-panels">
<div class="tab-panel panel-topic" markdown="1">

{% for cat in defined_categories %}
{% assign count = 0 %}
{% for activity in labs %}{% if activity.lab.categories contains cat %}{% assign count = count | plus: 1 %}{% endif %}{% endfor %}
{% if count > 0 %}
{% if forloop.first %}
<details open markdown="1">
{% else %}
<details markdown="1">
{% endif %}
<summary><strong>{{ cat }}</strong> ({{ count }} exercises)</summary>

{% for activity in labs %}{% if activity.lab.categories contains cat %}
[{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) ({{ activity.lab.duration }})<br>{{ activity.lab.description }}

{% endif %}{% endfor %}

</details>
{% endif %}
{% endfor %}

</div>
<div class="tab-panel panel-course" markdown="1">

{% for course in defined_courses %}
{% assign count = 0 %}
{% for activity in labs %}{% if activity.lab.courses contains course.id %}{% assign count = count | plus: 1 %}{% endif %}{% endfor %}
{% if count > 0 %}
<details markdown="1">
<summary><strong>{{ course.id }}: {{ course.name }}</strong> ({{ count }} exercises)</summary>

{% for activity in labs %}{% if activity.lab.courses contains course.id %}
[{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) ({{ activity.lab.duration }})<br>{{ activity.lab.description }}

{% endif %}{% endfor %}

</details>
{% endif %}
{% endfor %}

</div>
</div>

