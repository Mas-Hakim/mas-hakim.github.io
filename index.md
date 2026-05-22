---
layout: default
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

## 👤 About me

Живу на Бали, занимаюсь веб-разработкой и автоматизацией.  
В этом блоге пишу о:

- 🚀 Современном фронтенде (Astro, React, Jekyll)
- 🤖 Парсинге и автоматизации (Python, Bash)
- 🏝️ Жизни на Бали и полезных ресурсах

---

## 📬 Contacts

- **GitHub**: [Mas-Hakim](https://github.com/Mas-Hakim)
- **Email**: hakim@example.com (замените на реальный)
- **Telegram**: @mas_hakim (если есть)

---

<style>
  .posts-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 1.5rem;
    margin: 2rem 0;
  }

  .post-card {
    border: 1px solid rgba(255,255,255,0.2);
    padding: 1.2rem;
    border-radius: 8px;
    background: rgba(0,0,0,0.3);
    transition: transform 0.2s;
  }

  .post-card:hover {
    transform: translateY(-4px);
    border-color: rgba(255,255,255,0.4);
  }

  .post-card h3 {
    margin-top: 0;
    margin-bottom: 0.5rem;
  }

  .post-card h3 a {
    color: #fff;
    text-decoration: none;
  }

  .post-card h3 a:hover {
    text-decoration: underline;
  }

  .post-meta {
    color: #aaa;
    font-size: 0.85rem;
    margin-bottom: 0.8rem;
  }

  .read-more {
    display: inline-block;
    margin-top: 0.8rem;
    color: #0ff;
    text-decoration: none;
    font-weight: 500;
  }

  .read-more:hover {
    text-decoration: underline;
  }

  /* Для тёмной темы midnight */
  body {
    background: #111;
    color: #eee;
  }
</style>