# Laravel Octane + RoadRunner — Docker Boilerplate

Boilerplate для быстрого развертывания **Laravel Octane** на **RoadRunner** в Docker.

## Архитектура

```
┌─────────────────────────────────────────────────┐
│              Docker Compose                      │
│                                                  │
│  ┌──────────────────────┐   ┌────────────────┐  │
│  │  RoadRunner (PHP 8.5) │   │  PostgreSQL    │  │
│  │  Laravel Octane       │   │  18.2 Alpine   │  │
│  │  :8000 HTTP           │   │  :5432         │  │
│  │  :2114 Health Check   │   └────────────────┘  │
│  └──────────────────────┘                        │
│                              ┌────────────────┐  │
│  ┌──────────────────────┐   │  Redis          │  │
│  │  Node.js (dev only)  │   │  8.6 Alpine     │  │
│  │  Vite HMR :5173      │   │  :6379          │  │
│  └──────────────────────┘   └────────────────┘  │
│                                                  │
│  ┌──────────────────────┐                        │
│  │  pgAdmin (dev only)  │                        │
│  │  :8080               │                        │
│  └──────────────────────┘                        │
└─────────────────────────────────────────────────┘
```

## Ключевые отличия от Nginx + PHP-FPM

| Аспект             | Nginx + PHP-FPM       | RoadRunner (Octane)                      |
|--------------------|-----------------------|------------------------------------------|
| Контейнеры         | 2 (Nginx + PHP-FPM)   | 1 (RoadRunner)                           |
| Протокол           | Unix socket / FastCGI | Встроенный HTTP-сервер                   |
| Модель             | Процесс на запрос     | Persistent workers                       |
| Производительность | Хорошая               | Высокая (нет bootstrap на каждый запрос) |
| Статика            | Nginx                 | RoadRunner static plugin                 |
| Перезагрузка кода  | Автоматическая        | `make rr-reload` или `rr reset`          |

## Структура проекта (файлы boilerplate)

```
├── docker/
│   ├── php.Dockerfile          # Многоэтапный образ (dev + production)
│   └── php/
│       ├── php.ini             # Настройки PHP для разработки
│       └── php.prod.ini        # Настройки PHP для продакшена
├── .rr.yaml                    # Конфигурация RoadRunner
├── docker-compose.yml          # Базовые сервисы (app, postgres, redis)
├── docker-compose.dev.yml      # Оверлей для разработки (volumes, xdebug, pgadmin, node)
├── docker-compose.prod.yml     # Оверлей для продакшена (queue, scheduler, migrate)
├── .dockerignore               # Исключения из контекста сборки
├── .env.docker                 # Шаблон переменных окружения для Docker
├── Makefile                    # Команды управления проектом
└── SETUP.md                    # Подробная инструкция по установке
```

## Быстрый старт

```bash
# 1. Создайте Laravel проект
composer create-project laravel/laravel my-app

# 2. Установите Laravel Octane
cd my-app
composer require laravel/octane
php artisan octane:install --server=roadrunner

# 3. Скопируйте файлы boilerplate в проект
# (docker/, docker-compose*.yml, Makefile, .rr.yaml, .dockerignore)

# 4. Настройте .env (см. SETUP.md)

# 5. Запустите
make setup
```

Подробная инструкция — в файле **[SETUP.md](SETUP.md)**.

## Основные команды

| Команда                  | Описание                         |
|--------------------------|----------------------------------|
| `make setup`             | Полная инициализация проекта     |
| `make up`                | Запустить контейнеры (dev)       |
| `make down`              | Остановить контейнеры            |
| `make logs-app`          | Логи RoadRunner                  |
| `make shell`             | Войти в контейнер                |
| `make rr-reload`         | Перезагрузить воркеры RoadRunner |
| `make rr-workers`        | Статус воркеров                  |
| `make artisan CMD="..."` | Выполнить artisan-команду        |
| `make test-php`          | Запустить тесты                  |
| `make help`              | Полный список команд             |
