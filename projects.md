---
layout: default
title: Projects
description: Notable systems, tools, apps, and experiments by Mohammed Sazid Al Rashid.
---

{% assign projects = site.data.projects %}

<article class="projects-page">
  <header class="projects-header">
    <p class="projects-label">{{ projects.summary.eyebrow }}</p>
    <h1>{{ projects.summary.title }}</h1>
    <p class="projects-intro">{{ projects.summary.intro }}</p>
  </header>

  <ol class="project-timeline">
    {% for project in projects.projects %}
    <li class="project-event">
      <time>{{ project.period }}</time>
      <section class="project-item">
        <div class="project-item-heading">
          <h2>{{ project.name }}</h2>
          <p>{{ project.stack }}</p>
        </div>
        <p class="project-status">{{ project.status }}</p>
        <p class="project-summary">{{ project.summary }}</p>
        <ul class="project-details">
          {% for detail in project.details %}
          <li>{{ detail }}</li>
          {% endfor %}
        </ul>
        {% if project.showcase %}
        <div class="project-showcase">
          {% for item in project.showcase %}
          <figure class="project-showcase-item project-showcase-{{ item.type }}">
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
        <div class="project-links">
          {% for link in project.links %}
          <a href="{{ link.url }}">{{ link.label }}</a>
          {% endfor %}
        </div>
      </section>
    </li>
    {% endfor %}
  </ol>
</article>
