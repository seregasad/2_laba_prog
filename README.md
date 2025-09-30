# Практическая работа 2: Многоконтейнерные приложения (Docker Compose)

## 📁 Структура папки

- `lab_instructions.html` - Подробные инструкции (лонгрид, стиль как Практика 1)
- `cheatsheet.md` - Чит-шит с ключевыми командами и концепциями
- `README.md` - Этот файл
- `materials/starter/` - Готовый skeleton (Dockerfile, compose, .env.example, Makefile, минимальное FastAPI)

## 🚀 Быстрый старт

1) Добавьте записи в hosts (admin:
- Windows: C:\\Windows\\System32\\drivers\\etc\\hosts
- Linux/macOS: /etc/hosts)
```
127.0.0.1 api.localhost traefik.localhost adminer.localhost
```
2) Варианты старта:
- A) Скачать архив: `materials/starter.zip` и распаковать в текущую папку
- B) Скопировать skeleton из папки `materials/starter/`
```
cp -r materials/starter/* .
cp .env.example .env
# при Compose v1 используйте docker-compose
# при v2 используйте docker compose
docker compose up -d --build
```

4) make check (опционально):
```
make check
# быстрый преполёт (опционально)
# проверит наличие Docker/Compose, занятые порты и hosts
bash materials/starter/scripts/doctor.sh

```
5) Очистка/сброс стенда (при залипших томах/сетях):
```
make clean   # интерактивное подтверждение
# или полный цикл
make reset
```

> Примечание: Makefile — опционален. Если у вас нет `make` (или `jq` для некоторых удобных целей), используйте эквивалентные команды `docker compose` из разделов выше.

## 🛠️ Если нет make: эквиваленты команд

| Действие | Make | Эквивалент docker compose |
|---|---|---|
| Сборка и запуск | `make up` | `docker compose up -d --build` |
| Остановить | `make down` | `docker compose down` |
| Логи API | `make logs-api` | `docker compose logs -f --tail=200 api` |
| Проверка health | `make check` | `bash scripts/check.sh` |
| Полная очистка | `make reset` | `docker compose down -v && docker network prune -f && docker volume prune -f` |



3) Проверьте:
```
curl -s http://api.localhost/healthz
```

## 🎯 Цели

- Освоить Compose-сети (public/backend), Traefik-роутинг по Host(`*.localhost`)
- Собрать безопасный образ (multi-stage, non-root, healthcheck)
- Настроить зависимость запуска (healthcheck + depends_on)
- Научиться искать и устранять типовые барьеры запуска (WSL2, порты, v1/v2 compose)


## 📖 Как использовать

1. Откройте `lab_instructions.html` в браузере — это основной документ задания
2. Используйте разделы и чек-лист для пошагового выполнения
3. При необходимости загляните в `cheatsheet.md`
4. Добавляйте собственные заметки/ссылки в `REPORT.md`

## 🔗 Связанные материалы

- Предыдущая: [1]
- Следующая: [3]
## 📈 Observability профиль (Prometheus + Grafana + cAdvisor)
```
docker compose -f docker-compose.yml -f docker-compose.observability.yml up -d
```
- Grafana: http://grafana.localhost (admin/admin — смените)
- Prometheus: http://prometheus.localhost

## 🔐 Traefik: basic auth + rate limit (опционально)
1) Сгенерируйте пользователя:
```
docker run --rm httpd:2.4-alpine htpasswd -nbB admin 'yourpass'
```

> Примечание (WSL2/cAdvisor): на некоторых конфигурациях cAdvisor может не видеть все метрики. Это не влияет на выполнение лабы — используйте панели Prometheus/Grafana.

2) Вставьте строку в .env как BASIC_AUTH_USERS=...
3) Запустите с override:
```
docker compose -f docker-compose.yml -f docker-compose.security.yml up -d
```


## 🧱 Частые барьеры и быстрые решения
- Порт 80 занят — временно меняйте на 8080: `ports: ["8080:80"]`
- hosts не правится — Windows: откройте блокнот от администратора; Linux/macOS: `sudo nano /etc/hosts`
- WSL2: храните проект в файловой системе WSL (например, `~/work`), перезапускайте Docker Desktop при ошибках сокета
- Compose v1: используйте `docker-compose` (или установите v2)
- DNS `*.localhost` не срабатывает — добавьте явные записи в hosts

