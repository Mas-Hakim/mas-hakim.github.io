---
title: "Master SEO Strategy & Entity Architecture: Semantic SSoT (Final Edition)"
---

Данный документ представляет собой финальный стандарт архитектуры связанных данных (Linked Data) для цифровой экосистемы, объединяющей персональный бренд (Person) и коммерческую структуру (Organization).

---

### 1. Архитектура графа сущностей (JSON-LD Blueprint)

Эталонный скрипт `@graph` для внедрения в `<head>` на `mas-hakim.github.io` и `company-domain.com`.

```json
{
  "@context": "https://schema.org",
  "@graph": [
    {
      "@type": "Person",
      "@id": "https://mas-hakim.github.io",
      "name": "Mas Hakim",
      "url": "https://mas-hakim.github.io",
      "email": "root-email@company-domain.com",
      "sameAs": [
        "https://linkedin.com",
        "https://github.com",
        "https://vk.com",
        "https://twitter.com"
      ],
      "jobTitle": "Founder",
      "worksFor": { "@id": "https://company-domain.com" },
      "contactPoint": [
        {
          "@type": "ContactPoint",
          "telephone": "+7-XXX-XXX-XX-XX",
          "contactType": "personal",
          "url": "https://t.me"
        }
      ]
    },
    {
      "@type": "Organization",
      "@id": "https://company-domain.com",
      "name": "Legal Name LLC",
      "legalName": "Полное юридическое наименование",
      "url": "https://company-domain.com",
      "logo": "https://company-domain.com",
      "taxID": "ИНН_ЗДЕСЬ",
      "founder": { "@id": "https://mas-hakim.github.io" },
      "address": {
        "@type": "PostalAddress",
        "streetAddress": "Улица, дом, офис",
        "addressLocality": "Город",
        "addressCountry": "RU"
      },
      "contactPoint": [
        {
          "@type": "ContactPoint",
          "telephone": "+7-800-XXX-XX-XX",
          "contactType": "customer service",
          "email": "info@company-domain.com",
          "availableLanguage": ["Russian", "English"]
        }
      ],
      "sameAs": [
        "https://facebook.com",
        "https://instagram.com"
      ]
    },
    {
      "@type": "WebSite",
      "@id": "https://company-domain.com",
      "url": "https://company-domain.com",
      "publisher": { "@id": "https://company-domain.com" }
    }
  ]
}
```

---

### 2. Реестр ключей Schema.org (Schema Keys Table)

| Ключ (Property) | Сущность | Описание (ru_RU) | Статус / Валидация |
| :--- | :--- | :--- | :--- |
| `@id` | Все узлы | Постоянный IRI объекта. | Обязателен для Entity Linking. |
| `sameAs` | Person / Org | Массив внешних ссылок (Social). | Синхронизация профилей в Knowledge Graph. |
| `founder` | Organization | Указание на создателя (Person). | Фундамент E-E-A-T (связь авторитетов). |
| `taxID` | Organization | Официальный налоговый номер. | Верификация легитимности бизнеса. |
| `contactPoint` | Person / Org | Каналы связи (Phone, TG, Email). | Увеличение Trust-фактора. |
| `mainEntityOfPage` | WebPage | Объект, которому посвящена страница. | Уточнение интента для Google/Yandex. |

---

### 3. Техническое задание на визуализацию (Diagram Specs)

#### ТЗ на Диаграмму №1: Архитектура узлов (Hierarchy)
*   **Тип:** Node-link diagram.
*   **Центральный узел:** Person (Master Identity).
*   **Дочерние узлы:** Organization, Personal Blog, GitHub Assets.
*   **Связи:**
    *   `Person` -> `worksFor` -> `Organization`.
    *   `Organization` -> `founder` -> `Person`.
    *   `WebSite` -> `publisher` -> `Organization`.
*   **Цель:** Показать отсутствие слияния URL при сохранении логической связи владельца и бизнеса.

#### ТЗ на Диаграмму №2: Стратегия кросслинковки (Link Closing)
*   **Тип:** Flowchart / Cycle.
*   **Компоненты:** GMB (CID), Yandex Business, Main Site, GitHub Blog.
*   **Логика замыкания:**
    1.  GMB подтверждает локацию -> Ссылка на Main Site.
    2.  Main Site JSON-LD содержит `@id` Person.
    3.  Person JSON-LD содержит `sameAs` на GitHub Blog.
    4.  GitHub Blog содержит `author` -> `@id` Person.
*   **Результат:** Любая статья в блоге получает вес от физически подтвержденной организации через цепочку метаданных.

---

### 4. SEO Strategy: Управление сущностями (Entity Management)

1.  **Принцип Изоляции:** Коммерческие запросы "купить/заказать" направляются на `company-domain.com`. Информационные и экспертные запросы "как сделать/аналитика" — на `mas-hakim.github.io`.
2.  **Верификация через ContactPoint:** Указание одного и того же верифицированного номера телефона и Root Email во всех профилях (Google, Yandex, Social Media) создает неявный сигнал для алгоритмов о принадлежности всех ресурсов одному лицу.
3.  **Метод SSoT:** Любые изменения в юридических данных или социальных профилях вносятся сначала в JSON-LD на Root Identity (`/#person`), после чего синхронизируются на остальных ресурсах. Это исключает противоречивость данных (Data Conflict), негативно влияющую на E-E-A-T.
