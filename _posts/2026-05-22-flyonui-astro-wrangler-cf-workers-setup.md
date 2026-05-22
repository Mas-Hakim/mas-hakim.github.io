---
layout: post
title: "FlyonUI + Astro + Wrangler + Cloudflare Workers локально в Portainer: инструкция"
date: 2026-05-22 15:45:00 +0300
categories: [devops, frontend, deployment]
tags: [astro, cloudflare, workers, wrangler, portainer, docker, flyonui]
author: "mas Hakim"
---

## Обзор стека

Этот гайд покажет, как настроить полноценную dev среду локально в Portainer для:

- **FlyonUI** — Modern React UI component library
- **Astro** — Static site generator с Island Architecture
- **Wrangler** — CLI для Cloudflare Workers
- **Cloudflare Workers** — Serverless edge computing platform
- **Portainer** — Docker management UI

---

## Часть 1: Подготовка Docker стека

### 1.1 Структура проекта

```
my-astro-project/
├── docker-compose.yml
├── Dockerfile
├── .dockerignore
├── wrangler.toml
├── src/
│   ├── pages/
│   ├── components/
│   └── layouts/
├── functions/
│   └── api.ts
└── astro.config.mjs
```

### 1.2 Dockerfile

```dockerfile
FROM node:20-alpine

# Устанавливаем необходимые пакеты
RUN apk add --no-cache git python3 make g++

WORKDIR /app

# Копируем package.json и устанавливаем зависимости
COPY package*.json ./
RUN npm ci --prefer-offline --no-audit

# Копируем остальной код
COPY . .

# Экспонируем порты
EXPOSE 3000 8787

# Запускаем dev сервер с Wrangler
CMD ["npm", "run", "dev"]
```

### 1.3 docker-compose.yml

```yaml
version: '3.8'

services:
  astro-dev:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: astro-flyonui-dev
    ports:
      - "3000:3000"  # Astro dev server
      - "8787:8787"  # Wrangler dev server
    volumes:
      - .:/app
      - /app/node_modules  # Избегаем перезаписи node_modules
    environment:
      - NODE_ENV=development
      - ASTRO_TELEMETRY_DISABLED=1
    networks:
      - dev-network
    restart: unless-stopped

  # Опционально: PostgreSQL для бэкэнда
  postgres:
    image: postgres:16-alpine
    container_name: astro-postgres
    environment:
      POSTGRES_USER: dev
      POSTGRES_PASSWORD: devpass
      POSTGRES_DB: astro_dev
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - dev-network

networks:
  dev-network:
    driver: bridge

volumes:
  postgres_data:
```

### 1.4 .dockerignore

```
node_modules
npm-debug.log
.git
.gitignore
.env.local
.vscode
.idea
dist/
.DS_Store
.wrangler/
```

---

## Часть 2: Настройка Astro проекта

### 2.1 package.json скрипты

```json
{
  "name": "astro-flyonui-workers",
  "version": "0.0.1",
  "description": "Astro + FlyonUI + Cloudflare Workers stack",
  "scripts": {
    "dev": "astro dev --host 0.0.0.0 --port 3000 & wrangler dev --port 8787",
    "dev:astro": "astro dev --host 0.0.0.0 --port 3000",
    "dev:wrangler": "wrangler dev --port 8787",
    "build": "astro build",
    "preview": "astro preview",
    "deploy:cf": "wrangler deploy"
  },
  "dependencies": {
    "astro": "^4.15.0",
    "@flyonui/ui": "^1.0.0",
    "react": "^18.3.1",
    "react-dom": "^18.3.1"
  },
  "devDependencies": {
    "wrangler": "^3.80.0",
    "typescript": "^5.4.0"
  }
}
```

### 2.2 astro.config.mjs

```javascript
import { defineConfig } from 'astro/config';
import react from '@astrojs/react';

export default defineConfig({
  integrations: [react()],
  output: 'hybrid',  // Для комбинации SSG и SSR
  server: {
    host: '0.0.0.0',
    port: 3000,
  },
  vite: {
    server: {
      hmr: {
        host: 'localhost',
        port: 3000,
      },
    },
  },
});
```

### 2.3 Пример компонента с FlyonUI

```astro
---
import { Button, Card, Input } from '@flyonui/ui';

interface Props {
  title: string;
  description: string;
}

const { title, description } = Astro.props;
---

<Card>
  <div class="p-6">
    <h2 class="text-2xl font-bold mb-2">{title}</h2>
    <p class="text-gray-600 mb-4">{description}</p>
    <Button variant="primary">Узнать больше</Button>
  </div>
</Card>

<style>
  :global(body) {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto;
  }
</style>
```

---

## Часть 3: Настройка Cloudflare Workers + Wrangler

### 3.1 wrangler.toml

```toml
# Основные настройки
name = "astro-flyonui-api"
main = "functions/api.ts"
compatibility_date = "2024-05-22"
node_compat = true

# API routes (для dev локально)
[[routes]]
pattern = "/api/*"
zone_name = "example.com"  # Замени на свой домен при деплое

# KV Namespace (опционально)
[[kv_namespaces]]
binding = "CACHE"
id = "your_kv_namespace_id"
preview_id = "your_preview_id"

# Environment variables
[env.development]
vars = { ENVIRONMENT = "development", DEBUG = true }

[env.production]
vars = { ENVIRONMENT = "production", DEBUG = false }
route = "api.example.com/*"
```

### 3.2 Пример Worker функции

```typescript
// functions/api.ts

export interface Env {
  CACHE: KVNamespace;
}

export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const url = new URL(request.url);
    
    // GET /api/posts
    if (url.pathname === '/api/posts' && request.method === 'GET') {
      // Проверяем кэш
      const cached = await env.CACHE.get('posts');
      if (cached) {
        return new Response(cached, {
          headers: { 'Content-Type': 'application/json' },
        });
      }
      
      // Fetch данные
      const posts = [
        { id: 1, title: 'Post 1', date: new Date().toISOString() },
        { id: 2, title: 'Post 2', date: new Date().toISOString() },
      ];
      
      // Кэшируем на 1 час
      await env.CACHE.put('posts', JSON.stringify(posts), {
        expirationTtl: 3600,
      });
      
      return new Response(JSON.stringify(posts), {
        headers: { 'Content-Type': 'application/json' },
      });
    }
    
    return new Response('Not Found', { status: 404 });
  },
};
```

---

## Часть 4: Запуск в Portainer

### 4.1 Создание Stack в Portainer

1. Открой **Portainer** → **Stacks** → **Add Stack**
2. Выбери **Web editor**
3. Скопируй содержимое `docker-compose.yml` выше
4. Нажми **Deploy**

### 4.2 Проверка статуса

```bash
# Внутри контейнера
docker exec astro-flyonui-dev npm run dev

# Или через Portainer:
# Containers → astro-flyonui-dev → Logs
```

### 4.3 Доступ к сервисам

| Сервис | URL | Назначение |
|--------|-----|----------|
| Astro Dev | `http://localhost:3000` | Frontend + SSG |
| Wrangler Dev | `http://localhost:8787` | Workers API |
| PostgreSQL | `localhost:5432` | База данных |

---

## Часть 5: Разработка и Hot Reload

### 5.1 HMR в Docker

Для корректной работы Hot Module Replacement (HMR) убедись, что в `astro.config.mjs`:

```javascript
vite: {
  server: {
    hmr: {
      host: 'localhost',  // Или IP хоста
      port: 3000,
    },
  },
},
```

### 5.2 Тестирование API Workers локально

```bash
# В контейнере
curl http://localhost:8787/api/posts

# Или в другом контейнере
docker exec astro-flyonui-dev curl http://localhost:8787/api/posts
```

### 5.3 Интеграция Astro с Workers API

```astro
---
// src/pages/posts.astro
interface Post {
  id: number;
  title: string;
  date: string;
}

let posts: Post[] = [];

try {
  const response = await fetch('http://localhost:8787/api/posts');
  posts = await response.json();
} catch (error) {
  console.error('Failed to fetch posts:', error);
}
---

<div class="posts-list">
  {posts.map(post => (
    <article key={post.id}>
      <h3>{post.title}</h3>
      <time>{new Date(post.date).toLocaleDateString()}</time>
    </article>
  ))}
</div>
```

---

## Часть 6: Отладка

### 6.1 Основные команды

```bash
# Лог контейнера
docker logs -f astro-flyonui-dev

# Интерактивный терминал
docker exec -it astro-flyonui-dev sh

# Проверка портов
docker exec astro-flyonui-dev netstat -tuln

# Перебилд контейнера
docker-compose build --no-cache
```

### 6.2 Частые проблемы

**Проблема: Port 3000 already in use**
```bash
# Решение
docker-compose down
docker-compose up --build
```

**Проблема: node_modules не обновляются**
```bash
# Решение
docker-compose down -v  # Удаляет volume
docker-compose up --build
```

**Проблема: HMR не работает**
```bash
# Убедись, что host правильный в astro.config.mjs
# Для локальной разработки используй localhost
# Для удалённого доступа используй IP машины
```

---

## Часть 7: Deploy на Cloudflare

### 7.1 Аутентификация

```bash
# В контейнере
wrangler login

# Или передай токен
export CLOUDFLARE_API_TOKEN=your_token
```

### 7.2 Deploy

```bash
# Production build
npm run build

# Deploy Workers
npm run deploy:cf

# Или через Wrangler
wrangler deploy
```

### 7.3 wrangler.toml для продакшена

```toml
[env.production]
route = "api.yourdomain.com/*"
zone_id = "your_zone_id"

[[env.production.kv_namespaces]]
binding = "CACHE"
id = "production_kv_id"
```

---

## Полезные ссылки

- [Astro Documentation](https://docs.astro.build)
- [FlyonUI Documentation](https://flyonui.com)
- [Wrangler CLI Documentation](https://developers.cloudflare.com/workers/wrangler/)
- [Cloudflare Workers Documentation](https://developers.cloudflare.com/workers/)
- [Docker Documentation](https://docs.docker.com)
- [Portainer Documentation](https://docs.portainer.io)

---

## Заключение

Теперь у тебя есть:

✅ **Локальная dev среда** в Docker/Portainer
✅ **Полная интеграция** Astro + FlyonUI + Workers
✅ **Hot reload** для быстрой разработки
✅ **Готовый к deploy** стек на Cloudflare
✅ **KV Namespace** для кэширования данных

Этот стек идеален для:
- 🚀 Высокопроизводительных фронтенд приложений
- ⚡ Serverless бэкэндов на Workers
- 💄 Modern UI с FlyonUI компонентами
- 🐳 Контейнеризованной разработки

Удачи в разработке! 🎉
