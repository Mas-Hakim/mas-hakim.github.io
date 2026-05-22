---
layout: home
title: Home
---

# Добро пожаловать в B.O.R.S.H

<div style="text-align: center; margin: 2rem 0;">
  <img src="./images/main.webp" alt="Bali" style="max-width: 100%; border-radius: 8px;">
</div>

> **Bali Open Re-Sources Hub** — открытое сообщество для обмена знаниями, опытом и ресурсами на Бали.

Привет! Меня зовут **Mas Hakim**. Я живу на Бали уже 12 лет и делюсь заметками о веб-разработке, DevOps, парсинге и жизни на острове.

---

## 📝 Последние заметки

<div class="posts-grid">
  {% assign recent_posts = site.posts | sort: 'date' | reverse | limit: 3 %}
  {% for post in recent_posts %}
    <div class="post-card">
      <h3>
        <a href="{{ post.url }}">{{ post.title }}</a>
      </h3>
      <div class="post-meta">
        <time>{{ post.date | date: "%d.%m.%Y" }}</time>
      </div>
      <p>{{ post.excerpt | strip_html | truncatewords: 20 }}</p>
      <a href="{{ post.url }}" class="read-more">Читать →</a>
    </div>
  {% endfor %}
</div>

---



