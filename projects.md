---
layout: default
title: Projects
description: Systems projects, developer tools, Android apps, university work and experiments by Mohammed Sazid Al Rashid.
---

{% assign projects = site.data.projects %}

<h1>{{ projects.summary.title }}</h1>

<p>{{ projects.summary.intro }}</p>

{% for project in projects.projects %}
<section class="project">
  <h2>{{ project.name }}</h2>
  <p class="meta">{{ project.period }} · {{ project.status }} · {{ project.stack }}</p>
  <p>{{ project.summary }}</p>
  <ul>
    {% for detail in project.details %}
    <li>{{ detail }}</li>
    {% endfor %}
  </ul>
  {% if project.showcase %}
  <div class="showcase">
    {% for item in project.showcase %}
    <figure class="showcase-{{ item.type }}">
      {% if item.type == "output" %}
      <figcaption>{{ item.label }}</figcaption>
      <pre><code>{{ item.code | escape }}</code></pre>
      {% else %}
      {% assign media_href = item.href | default: item.src %}
      <a href="{{ media_href }}" target="_blank" rel="noopener noreferrer">
        <img src="{{ item.src }}" alt="{{ item.alt | default: item.label }}" loading="lazy">
      </a>
      <figcaption>{{ item.label }}</figcaption>
      {% endif %}
    </figure>
    {% endfor %}
  </div>
  {% endif %}
  <p class="project-links">
    {% for link in project.links %}
    <a href="{{ link.url }}">{{ link.label }}</a>
    {% endfor %}
  </p>
</section>
{% endfor %}
