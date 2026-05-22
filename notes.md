---
layout: base
title: Notes
permalink: /blog/
---

# Notes & Blog

> Коллекция заметок, статей и гайдов о веб-разработке, DevOps и открытых технологиях.

---

{% if site.posts.size > 0 %}
  <div class="posts-list">
    {% for post in site.posts %}
      <article class="post-item">
        <h3>
          <a href="{{ post.url }}">{{ post.title }}</a>
        </h3>
        
        <div class="post-meta">
          <time datetime="{{ post.date | date_to_xmlschema }}">
            {{ post.date | date: "%d.%m.%Y" }}
          </time>
          
          {% if post.categories.size > 0 %}
            <span class="divider">•</span>
            <span class="categories">
              {% for category in post.categories %}
                <code>{{ category }}</code>
              {% endfor %}
            </span>
          {% endif %}
        </div>
        
        {% if post.excerpt %}
          <p class="post-excerpt">{{ post.excerpt | strip_html | truncatewords: 30 }}</p>
        {% endif %}
        
        {% if post.tags.size > 0 %}
          <div class="post-tags">
            {% for tag in post.tags %}
              <span class="tag">#{{ tag }}</span>
            {% endfor %}
          </div>
        {% endif %}
      </article>
    {% endfor %}
  </div>
{% else %}
  <p class="no-posts">Постов пока нет. Скоро будут! 🚀</p>
{% endif %}


code {
  background: rgba(87, 87, 87, 0.2);
  padding: 0.2rem 0.4rem;
  border-radius: 2px;
}
</style>
