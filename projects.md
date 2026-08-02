---
layout: default
title: Projects
description: Notable systems, tools, apps, and experiments by Mohammed Sazid Al Rashid.
---

{% assign projects = site.data.projects %}

<article>
  <h1>{{ projects.summary.title }}</h1>

  <div class="prose">
    <p>{{ projects.summary.intro }}</p>
  </div>

  {% for project in projects.projects %}
  <section class="entry">
    <div class="entry-head">
      <h2>{{ project.name }}</h2>
      <p class="entry-meta">{{ project.stack }}</p>
    </div>
    <p class="entry-period">{{ project.period }} &middot; {{ project.status }}</p>
    <p>{{ project.summary }}</p>
    <ul class="entry-list">
      {% for detail in project.details %}
      <li>{{ detail }}</li>
      {% endfor %}
    </ul>

    {% if project.showcase %}
    <div class="showcase">
      {% for item in project.showcase %}
      <figure class="showcase-item showcase-{{ item.type }}">
        {% if item.type == "output" %}
        <figcaption>{{ item.label }}</figcaption>
        <pre><code>{{ item.code | escape }}</code></pre>
        {% else %}
        {% assign media_href = item.href | default: item.src %}
        <a href="{{ media_href }}">
          <img src="{{ item.src }}" alt="{{ item.alt | default: item.label }}" loading="lazy">
        </a>
        <figcaption>{{ item.label }}</figcaption>
        {% endif %}
      </figure>
      {% endfor %}
    </div>
    {% endif %}

    <div class="link-row">
      {% for link in project.links %}
      <a href="{{ link.url }}">{{ link.label }}</a>
      {% endfor %}
    </div>
  </section>
  {% endfor %}
</article>
