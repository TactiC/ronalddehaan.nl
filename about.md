---
layout: page
title: About
permalink: /about/
---

This is my personal blog. 

-------------------------
More me on the web:

<ul>
  {% if site.twitter_username %}
  <li>
    <a href="https://twitter.com/{{ site.twitter_username }}">
      <i class="fa fa-twitter"></i>
      <span class="username"> twitter</span>
    </a>
  </li>
  {% endif %}

  {% if site.github_username %}
  <li>
    <a href="https://github.com/{{ site.github_username }}">
      <i class="fa fa-github"></i>
      <span class="username"> github</span>
    </a>
  </li>
  {% endif %}

  {% if site.linkedin_username %}
  <li>
    <a href="https://nl.linkedin.com/in/{{ site.linkedin_username }}">
      <i class="fa fa-linkedin"></i>
      <span class="username"> linkedin</span>
    </a>
  </li>
  {% endif %}

  {% if site.untapped_username %}
  <li>
    <a href="https://untappd.com/user/{{ site.untappd_username }}">
      <i class="fa fa-beer"></i>
      <span class="username"> untappd</span>
    </a>
  </li>
  {% endif %}
</ul>
