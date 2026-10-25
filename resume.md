---
layout: default
title: CV
description: Public CV for Mohammed Sazid Al Rashid.
---

{% assign resume = site.data.resume %}

<article>
  <h1>{{ resume.name }}</h1>

  <ul class="meta-list">
    <li>{{ resume.location }}</li>
    <li><a href="{{ resume.website.url }}">{{ resume.website.label }}</a></li>
    {% for profile in resume.profiles %}
    <li><a href="{{ profile.url }}">{{ profile.label }}</a></li>
    {% endfor %}
  </ul>

  <p><a class="button" href="{{ resume.pdf }}">Download PDF</a></p>

  <h2 class="section-label">Summary</h2>
  <div class="prose">
    <p>{{ resume.summary }}</p>
  </div>

  <h2 class="section-label">Technical proficiencies</h2>
  <div class="sheet-wrap" tabindex="0" role="region" aria-label="Technical proficiencies">
    <table class="sheet sheet--rows">
      <caption class="visually-hidden">Technical proficiencies by category</caption>
      <tbody>
        {% for skill in resume.skills %}
        <tr>
          <th scope="row">{{ skill.label }}</th>
          <td>{{ skill.details }}</td>
        </tr>
        {% endfor %}
      </tbody>
    </table>
  </div>

  <h2 class="section-label">Experience</h2>
  {% for job in resume.experience %}
  <section class="entry">
    <div class="entry-head">
      <h3>{{ job.role }}</h3>
      <p class="entry-meta">{{ job.company }} / {{ job.location }}</p>
    </div>
    <p class="entry-period">{{ job.period }}</p>
    <ul class="entry-list">
      {% for highlight in job.highlights %}
      <li>{{ highlight }}</li>
      {% endfor %}
    </ul>
  </section>
  {% endfor %}

  <h2 class="section-label">Projects</h2>
  {% for project in resume.projects %}
  <section class="entry">
    <div class="entry-head">
      <h3>{{ project.name }}</h3>
      <p class="entry-meta">{{ project.stack }}</p>
    </div>
    <p class="entry-period"><a href="{{ project.link.url }}">{{ project.link.label }}</a></p>
    <ul class="entry-list">
      {% for highlight in project.highlights %}
      <li>{{ highlight }}</li>
      {% endfor %}
    </ul>
  </section>
  {% endfor %}

  <h2 class="section-label">Open source contributions</h2>
  <div class="sheet-wrap" tabindex="0" role="region" aria-label="Open source contributions">
    <table class="sheet">
      <caption class="visually-hidden">Open source contributions</caption>
      <thead>
        <tr>
          <th scope="col">Project</th>
          <th scope="col">Contribution</th>
        </tr>
      </thead>
      <tbody>
        {% for item in resume.open_source %}
        <tr>
          <th scope="row">{{ item.project }}</th>
          <td><a href="{{ item.url }}">{{ item.label }}</a></td>
        </tr>
        {% endfor %}
      </tbody>
    </table>
  </div>

  <h2 class="section-label">Talks and events</h2>
  <div class="sheet-wrap" tabindex="0" role="region" aria-label="Talks and events">
    <table class="sheet">
      <caption class="visually-hidden">Talks and events</caption>
      <thead>
        <tr>
          <th scope="col">Role</th>
          <th scope="col">Event</th>
          <th scope="col">Date</th>
        </tr>
      </thead>
      <tbody>
        {% for item in resume.talks %}
        <tr>
          <th scope="row">{{ item.role }}</th>
          <td>{{ item.event }}</td>
          <td>{{ item.date }}</td>
        </tr>
        {% endfor %}
      </tbody>
    </table>
  </div>

  <h2 class="section-label">Education</h2>
  {% for item in resume.education %}
  <section class="entry">
    <div class="entry-head">
      <h3>{{ item.institution }}</h3>
      <p class="entry-meta">{{ item.degree }}</p>
    </div>
    <p class="entry-period">{{ item.period }}</p>
    <ul class="entry-list">
      {% for highlight in item.highlights %}
      <li>{{ highlight }}</li>
      {% endfor %}
    </ul>
  </section>
  {% endfor %}
</article>
