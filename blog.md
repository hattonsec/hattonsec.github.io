---
layout: default
title: Payloads and Perspectives
permalink: /blog/
---

<section class="blog-index" style="padding: 2rem 0;">
  <div class="post-list" style="grid-template-columns: 1fr;">
    {% for post in site.posts %}
      {% unless post.visible == false %}
        <a href="{{ post.url | relative_url }}" class="post-card">
          <div class="post-meta">{{ post.date | date: "%b %d, %Y" }}</div>
          <h4 class="post-link" style="margin-top:0;">{{ post.title }}</h4>
          <div class="post-excerpt">
            {{ post.description | default: post.excerpt | strip_html | truncatewords: 40 }}
          </div>
        </a>
      {% endunless %}
    {% endfor %}
  </div>
</section>
