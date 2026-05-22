---
layout: default
title: Notes
---

# 📝 Notes & Blog

> Коллекция заметок, статей и гайдов о веб-разработке, DevOps и открытых технологиях.

---

{% assign posts = site.static_files | where: "extname", ".md" | where: "dir", "/blog" %}

{% if posts.size > 0 %}
<div class="posts-list">
{% for post in site.posts %}
  {% if post.url contains '/blog/' %}
  <article class="post-item" style="border: 1px solid rgba(87, 87, 87, 0.3); padding: 1.5rem; margin-bottom: 1.5rem; border-radius: 4px;">
    <h3 style="margin-top: 0;">
      <a href="{{ post.url }}">{{ post.title }}</a>
    </h3>
    <div class="post-meta" style="color: #888; font-size: 0.9rem; margin-bottom: 0.5rem;">
      <time datetime="{{ post.date | date_to_xmlschema }}">
        {{ post.date | date: "%d.%m.%Y" }}
      </time>
      {% if post.categories %}
        <span class="divider" style="margin: 0 0.5rem;">•</span>
        <span class="categories">
          {% for category in post.categories %}
            <code style="background: rgba(87, 87, 87, 0.2); padding: 0.2rem 0.4rem; border-radius: 2px;">{{ category }}</code>
          {% endfor %}
        </span>
      {% endif %}
    </div>
    {% if post.excerpt %}
    <p class="post-excerpt" style="margin: 0.5rem 0; color: #ccc;">{{ post.excerpt | strip_html | truncatewords: 30 }}</p>
    {% endif %}
    {% if post.tags %}
    <div class="post-tags" style="margin-top: 0.5rem;">
      {% for tag in post.tags %}
        <span class="tag" style="display: inline-block; background: rgba(87, 87, 87, 0.1); padding: 0.3rem 0.6rem; margin-right: 0.3rem; border-radius: 3px; font-size: 0.85rem;">#{{ tag }}</span>
      {% endfor %}
    </div>
    {% endif %}
  </article>
  {% endif %}
{% endfor %}
</div>
{% else %}
<p style="color: #999; text-align: center; padding: 2rem;">
  Постов пока нет. Скоро будут! 🚀
</p>
{% endif %}

---

<style>
.posts-list {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.post-item {
  transition: all 0.3s ease;
}

.post-item:hover {
  border-color: rgba(87, 87, 87, 0.6);
  background-color: rgba(87, 87, 87, 0.05);
}

.post-item a {
  color: #0ff;
  text-decoration: none;
  font-weight: 600;
}

.post-item a:hover {
  text-decoration: underline;
}

.post-meta {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  gap: 0.5rem;
}

.post-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.3rem;
}

.tag {
  color: #0ff;
  opacity: 0.8;
}

.tag:hover {
  opacity: 1;
}
</style>
