---
layout: default
title: CV
description: Public CV for Mohammed Sazid Al Rashid.
---

{% assign resume = site.data.resume %}

<h1>{{ resume.name }}</h1>

<ul class="inline-list meta">
  <li>{{ resume.location }}</li>
  <li><a href="{{ resume.website.url }}">{{ resume.website.label }}</a></li>
  {% for profile in resume.profiles %}
  <li><a href="{{ profile.url }}">{{ profile.label }}</a></li>
  {% endfor %}
  <li><a href="{{ resume.pdf }}">Download PDF</a></li>
</ul>

<h2>Summary</h2>

<p>{{ resume.summary }}</p>

<h2>Skills</h2>

{% for skill in resume.skills %}
<p><strong>{{ skill.label }}</strong><br>{{ skill.details }}</p>
{% endfor %}

<h2>Experience</h2>

{% for job in resume.experience %}
<section class="entry">
  <h3>{{ job.role }}</h3>
  <p class="meta">{{ job.company }} · {{ job.location }} · {{ job.period }}</p>
  <ul>
    {% for highlight in job.highlights %}
    <li>{{ highlight }}</li>
    {% endfor %}
  </ul>
</section>
{% endfor %}

<h2>Projects</h2>

{% for project in resume.projects %}
<section class="entry">
  <h3>{{ project.name }}</h3>
  <p class="meta">{{ project.stack }} · <a href="{{ project.link.url }}">{{ project.link.label }}</a></p>
  <ul>
    {% for highlight in project.highlights %}
    <li>{{ highlight }}</li>
    {% endfor %}
  </ul>
</section>
{% endfor %}

<h2>Open source</h2>

<ul>
  {% for item in resume.open_source %}
  <li>{{ item.project }}: <a href="{{ item.url }}">{{ item.label }}</a></li>
  {% endfor %}
</ul>

<h2>Education</h2>

{% for item in resume.education %}
<section class="entry">
  <h3>{{ item.institution }}</h3>
  <p class="meta">{{ item.degree }} · {{ item.period }}</p>
  <ul>
    {% for highlight in item.highlights %}
    <li>{{ highlight }}</li>
    {% endfor %}
  </ul>
</section>
{% endfor %}
