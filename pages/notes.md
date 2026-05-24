---
layout: page
title: 'Notes : Blog'
permalink: /blog/
---
> Коллекция рассказов, заметок, статей и гайдов про Бали, про жизнь и про технологии. 

{% if site.posts.size > 0 %}
  <div>
    {% for post in site.posts %}
      <article>
        <h3>
          <a href="{{ post.url }}">{{ post.title }}</a>
        </h3>        
        <div>
          <time datetime="{{ post.date | date_to_xmlschema }}">
            {{ post.date | date: "%d.%m.%Y" }}
          </time>          
          {% if post.categories.size > 0 %}
            <span>•</span>
            <span>
              {% for category in post.categories %}
                <code>{{ category }}</code>
              {% endfor %}
            </span>
          {% endif %}
        </div>        
        {% if post.excerpt %}
          <p>{{ post.excerpt | strip_html | truncatewords: 30 }}</p>
        {% endif %}        
        {% if post.tags.size > 0 %}
          <div>
            {% for tag in post.tags %}
              <span >#{{ tag }}</span>
            {% endfor %}
          </div>
        {% endif %}
      </article>
    {% endfor %}
  </div>
{% else %}
  <p >Постов пока нет. Скоро будут! 🚀</p>
{% endif %}



