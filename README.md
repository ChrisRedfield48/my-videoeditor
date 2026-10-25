<div align="center">

# 🎬 Kimu VideoEditor (Local Setup)

**Мощный open-source веб-редактор видео с AI-функциями, адаптированный для локального запуска.**

[![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)](https://reactjs.org/)
[![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org/)

</div>

---

## 📖 О проекте

Этот репозиторий — моя локальная сборка веб-редактора **Kimu** (форк проекта [trykimu/videoeditor](https://github.com/trykimu/videoeditor)).

Оригинальный проект настроен для развертывания на Production-сервере. В ходе работы я адаптировал конфигурацию для стабильного запуска на локальной машине (macOS) через Docker, устранив ряд критических ошибок инициализации.

## 🛠️ Что было исправлено и доработано

Чтобы заставить проект работать локально, были внесены следующие изменения:

1. **Исправлен баг PostgreSQL 18:**
   * В `docker-compose.dev.yml` изменен путь монтирования тома (`/var/lib/postgresql` вместо `/var/lib/postgresql/data`). Официальный Docker-образ 18-й версии изменил структуру хранения данных, из-за чего контейнер базы данных бесконечно перезапускался.
2. **Адаптирован конфиг Nginx для локальной разработки:**
   * В `nginx.conf` убрана жесткая привязка к домену `trykimu.com`.
   * Отключен блок HTTPS (443) с SSL-сертификатами Let's Encrypt, так как локально их нет.
   * Весь трафик перенаправлен через HTTP (порт 80), настроен корректный прокси-пасс на контейнеры `frontend`, `fastapi` и `backend`.
3. **Настроено окружение для работы с облаком:**
   * Подключено хранилище Cloudflare R2 (для медиафайлов) и API-ключ Google Gemini (для AI-функций).

## 🚀 Быстрый старт (Локальный запуск)

### Требования
* Установленный [Docker](https://www.docker.com/products/docker-desktop/) и Docker Compose.
* Свободные порты: `80`, `5432`, `6379`.

### Инструкция по запуску

**1. Клонируйте репозиторий:**
```bash
git clone https://github.com/ВАШ_НИК/ВАШ_РЕПОЗИТОРИЙ.git
cd ВАШ_РЕПОЗИТОРИЙ
```

**2. Настройте переменные окружения:**
Создайте файл `.env` в корне проекта (он скрыт от Git в целях безопасности) и заполните его:
```env
# База данных и Redis
POSTGRES_PASSWORD=videoeditor
DATABASE_URL=postgresql://videoeditor:videoeditor@postgres:5432/videoeditor
REDIS_URL=redis://redis:6379

# Секреты (сгенерируйте: openssl rand -base64 32)
BETTER_AUTH_SECRET=ваш_сгенерированный_секрет
BETTER_AUTH_URL=http://localhost

# Cloudflare R2 (для хранения видео)
R2_ACCOUNT_ID=ваш_account_id
R2_ACCESS_KEY_ID=ваш_access_key
R2_SECRET_ACCESS_KEY=ваш_secret_key
R2_ASSETS_BUCKET=kimu-assets
R2_RENDERS_BUCKET=kimu-renders

# Google Gemini AI
GEMINI_API_KEY=ваш_api_ключ
```

**3. Запустите контейнеры:**
*(Обязательно используйте оба файла `-f`, чтобы подтянулась база данных из dev-конфига)*
```bash
docker compose -f docker-compose.yml -f docker-compose.dev.yml up -d
```

**4. Откройте приложение:**
Перейдите в браузере по адресу: **👉 [http://localhost](http://localhost)**

> 💡 **Примечание:** Первый запуск может занять несколько минут, пока Docker скачает образы (Postgres, Redis, Nginx) и соберет Python/Node.js зависимости.

## 🏗️ Архитектура проекта

Проект состоит из 6 микросервисов, общающихся внутри Docker-сети:

* **`frontend`** — Клиентская часть (React + Vite), работает на порту 3000.
* **`fastapi`** — Основной бэкенд на Python, обрабатывает логику приложения и API-запросы (порт 3000).
* **`backend`** — Сервис рендеринга видео на Node.js + Remotion (порт 8000).
* **`postgres`** — Реляционная база данных для хранения пользователей и проектов.
* **`redis`** — Кэш и брокер сообщений для очередей рендеринга.
* **`nginx`** — Reverse-proxy, который принимает запросы на порту 80 и распределяет их между остальными сервисами.

## 🖼️ Скриншоты

![Интерфейс редактора](interface.png)

---
<div align="center">
  <i>Оригинальный проект: <a href="https://github.com/trykimu/videoeditor">trykimu/videoeditor</a></i>
</div>