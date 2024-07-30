---
layout: default
title: Daily Blogs
---

<h1>Daily Blogs</h1>

<ul>
  {% assign daily_posts = site.posts | where: "tags", "Daily" %}
  {% for post in daily_posts %}
    <li>
      <a href="{{ post.url | relative_url}}">{{ post.title }}</a>
      <p>{{ post.excerpt }}</p> <!-- Optional: Show excerpt of the post -->
      <time datetime="{{ post.date | date_to_xmlschema }}">
        {{ post.date | date: "%b %d, %Y" }}
      </time>
    </li>
  {% else %}
    <li>No posts found for the "Daily" tag.</li>
  {% endfor %}
</ul>
