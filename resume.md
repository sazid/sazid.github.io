---
layout: default
title: CV
description: Public CV for Mohammed Sazid Al Rashid.
---

{% assign resume = site.data.resume %}

<article class="cv-page">
  <header class="cv-header">
    <p class="cv-label">CV / Resume</p>
    <h1>{{ resume.name }}</h1>
    <ul class="cv-meta">
      <li>{{ resume.location }}</li>
      <li><a href="{{ resume.website.url }}">{{ resume.website.label }}</a></li>
      {% for profile in resume.profiles %}
      <li><a href="{{ profile.url }}">{{ profile.label }}</a></li>
      {% endfor %}
    </ul>
    <p class="cv-download"><a href="{{ resume.pdf }}">Download PDF</a></p>
  </header>

  <section class="cv-section">
    <h2>Professional Summary</h2>
    <p>{{ resume.summary }}</p>
  </section>

  <section class="cv-section">
    <h2>Technical Proficiencies</h2>
    <dl class="cv-skill-list">
      {% for skill in resume.skills %}
      <div>
        <dt>{{ skill.label }}</dt>
        <dd>{{ skill.details }}</dd>
      </div>
      {% endfor %}
    </dl>
  </section>

  <section class="cv-section">
    <h2>Experience</h2>
    {% for job in resume.experience %}
    <section class="cv-entry">
      <div class="cv-entry-heading">
        <h3>{{ job.role }}</h3>
        <p>{{ job.company }} / {{ job.location }}</p>
      </div>
      <p class="cv-period">{{ job.period }}</p>
      <ul>
        {% for highlight in job.highlights %}
        <li>{{ highlight }}</li>
        {% endfor %}
      </ul>
    </section>
    {% endfor %}
  </section>

  <section class="cv-section">
    <h2>Projects</h2>
    {% for project in resume.projects %}
    <section class="cv-entry">
      <div class="cv-entry-heading">
        <h3>{{ project.name }}</h3>
        <p>{{ project.stack }}</p>
      </div>
      <p class="cv-period"><a href="{{ project.link.url }}">{{ project.link.label }}</a></p>
      <ul>
        {% for highlight in project.highlights %}
        <li>{{ highlight }}</li>
        {% endfor %}
      </ul>
    </section>
    {% endfor %}
  </section>

  <section class="cv-section">
    <h2>Education</h2>
    {% for item in resume.education %}
    <section class="cv-entry">
      <div class="cv-entry-heading">
        <h3>{{ item.institution }}</h3>
        <p>{{ item.degree }}</p>
      </div>
      <p class="cv-period">{{ item.period }}</p>
      <ul>
        {% for highlight in item.highlights %}
        <li>{{ highlight }}</li>
        {% endfor %}
      </ul>
    </section>
    {% endfor %}
  </section>
</article>
