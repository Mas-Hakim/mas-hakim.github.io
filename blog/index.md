---
layout: default
title: Notes
---

# 📝 Notes & Blog

> Коллекция заметок, статей и гайдов о веб-разработке, DevOps и открытых технологиях.

---

{% if site.posts.size > 0 %}
  <div class="posts-list">
    {% for post in site.posts %}
      {% if post.path contains 'blog/' %}
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
      {% endif %}
    {% endfor %}
  </div>
{% else %}
  <p class="no-posts">Постов пока нет. Скоро будут! 🚀</p>
{% endif %}

---

<style>
.posts-list {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.post-item {
  border: 1px solid rgba(87, 87, 87, 0.3);
  padding: 1.5rem;
  border-radius: 4px;
  transition: all 0.3s ease;
}

.post-item:hover {
  border-color: rgba(87, 87, 87, 0.6);
  background-color: rgba(87, 87, 87, 0.05);
}

.post-item h3 {
  margin-top: 0;
  margin-bottom: 0.5rem;
}

.post-item h3 a {
  color: #0ff;
  text-decoration: none;
  font-weight: 600;
}

.post-item h3 a:hover {
  text-decoration: underline;
}

.post-meta {
  color: #888;
  font-size: 0.9rem;
  margin-bottom: 0.5rem;
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  gap: 0.5rem;
}

.post-excerpt {
  margin: 0.5rem 0;
  color: #ccc;
}

.post-tags {
  margin-top: 0.5rem;
  display: flex;
  flex-wrap: wrap;
  gap: 0.3rem;
}

.tag {
  display: inline-block;
  background: rgba(87, 87, 87, 0.1);
  padding: 0.3rem 0.6rem;
  border-radius: 3px;
  font-size: 0.85rem;
  color: #0ff;
  opacity: 0.8;
}

.tag:hover {
  opacity: 1;
}

.no-posts {
  color: #999;
  text-align: center;
  padding: 2rem;
}

code {
  background: rgba(87, 87, 87, 0.2);
  padding: 0.2rem 0.4rem;
  border-radius: 2px;
}
</style>
