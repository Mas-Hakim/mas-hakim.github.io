# Архитектура Проекта: Astro-Bali-Ecosystem

## 1. Описание архитектуры и стека
*   **Движок сайта:** Astro (Static Site Generation - SSG).
*   **Контент:** MDX (для интерактивных карточек вилл) + MD (для простых постов).
*   **Хранение:** GitHub (публичный фронтенд) + Локальный HDD (исходники).
*   **Админка:** **Keystatic** (локальный режим). Это headless-CMS без базы данных, которая работает как интерфейс к твоим файлам. Cline поможет её кастомизировать.
*   **Авторизация (Auth):** **Authelia** или **Authentic**. Работает как Single Sign-On (SSO) для всех контейнеров через Nginx Proxy.
*   **База данных:** Существующая **PostgreSQL** (для хранения логов админки или метаданных, если решим уйти от чистой статики).

## 2. Конфигурация хоста (Проверка)
Перед запуском убедись, что переменные и пути активны:
```bash
# Проверка сетевой доступности в Tailscale
tailscale status | grep "1hakim"
# Проверка наличия pnpm
pnpm --version
# Проверка доступа к HDD
ls -ld /media/hakim/HDD/f-docker/my-web-server/pages/ghub/bali
```

## 3. Docker Compose для Astro + MDX
Этот контейнер только для разработки. Сборка (build) будет улетать на GitHub Pages.
```yaml
services:
  astro:
    image: node:20-slim
    container_name: astro-bali
    user: "1000:1000"
    working_dir: /app
    volumes:
      - /media/hakim/HDD/f-docker/my-web-server/pages/ghub/bali:/app
    ports:
      - "4321:4321"
    environment:
      - NODE_ENV=development
    command: >
      sh -c "corepack enable && pnpm install && pnpm run dev --host"
    restart: unless-stopped
```

## 4. Docker Compose для Admin + Auth (SSO)
Используем Authelia для защиты всех `*.bali` и других сервисов через Nginx.
```yaml
services:
  authelia:
    image: authelia/authelia:latest
    container_name: auth-service
    volumes:
      - /media/hakim/HDD/f-docker/authelia/config:/config
    networks:
      - tailscale_net
    restart: unless-stopped

  # Админка Keystatic встроена в Astro, отдельный контейнер не нужен,
  # она будет доступна по пути /keystatic внутри Astro контейнера.
```

## 5. Таск-лист по проекту Astro + MDX
- [ ] Инициализировать Astro в папке `/bali` через `pnpm create astro@latest`.
- [ ] Настроить `astro.config.mjs` для работы с GitHub Pages (base: '/bali').
- [ ] Перенести текущий `index.html` в `src/pages/index.astro`.
- [ ] Создать первую коллекцию в `src/content/blog` (формат .md).
- [ ] Настроить GitHub Actions для авто-деплоя при пуше.

## 6. Таск-лист для Admin + Auth + Cline (Postgres)
- [ ] Дать задачу **Cline**: "Интегрируй Keystatic в существующий Astro проект в Local Mode".
- [ ] Настроить авторизацию в Authelia, используя твой PostgreSQL как бэкенд для юзеров.
- [ ] Сконфигурировать Nginx (Sidecar), чтобы путь `/keystatic` требовал авторизации Authelia.
- [ ] Проверить сохранение файлов через админку (должны появляться в `src/content/`).

## 7. Таск-листы альтернатив (Jekyll / Hugo)
*Для понимания, если Astro покажется избыточным:*

### Jekyll (Встроенный в GitHub)
- [ ] Создать файл `_config.yml`.
- [ ] Создать папку `_posts`.
- [ ] Включить в настройках GitHub Pages "Jekyll" (не требует локального билда).

### Hugo (Сверхбыстрый)
- [ ] Установить Hugo бинарник.
- [ ] Выбрать тему (например, PaperMod).
- [ ] Настроить экспорт в папку `docs` для GitHub Pages.
