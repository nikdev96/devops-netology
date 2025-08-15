# 05‑virt‑03 — Docker Intro (отчёт по выполнению)

**Стенд:** Ubuntu (root@frequent-range), IP `109.120.157.49`  
**Репозиторий:** `nikdev96/devops-netology`  
**Ветка задания:** `05-virt-03-docker-intro`

Цель: установить Docker, запустить контейнеры, собрать свой образ, поднять Compose‑стек, выполнить инспекцию и показать export/save.

---

## Этап 1 — Установка и проверка Docker
Установлены `docker-ce`, `docker-ce-cli`, `containerd.io`, плагины `buildx` и `compose`. Тестовый контейнер `hello-world` успешно отработал.

**Артефакты**
- `answers/01_docker_version.txt` — версии Docker
- `answers/02_docker_info.txt` — системная информация Docker
- `answers/03_hello_world.txt` — вывод тестового контейнера

---

## Этап 2 — NGINX: порты и bind‑mount
- Контейнер `web` (образ `nginx:stable-alpine`) — публикуется на `8080→80`.
- Контейнер `webv` (образ `nginx:alpine`) с bind‑mount каталога сайта:
  ```
  /srv/devops-netology/05-virt-03-docker-intro/images/site -> /usr/share/nginx/html
  ```
  Отдаёт наш `index.html` на порту `8081`.

**Артефакты**
- `answers/04_nginx_200.txt` — заголовки `200 OK` с `8080`
- `answers/05_docker_ps.txt` — список контейнеров
- `answers/06_web_logs.txt` — логи контейнера `web`
- `answers/07_custom_index.html` — ответ с `8081`

---

## Этап 3 — Собственный образ
Dockerfile:
```dockerfile
FROM alpine:3.20
RUN apk add --no-cache bash
CMD ["bash","-lc","echo Hello from $(cat /etc/alpine-release)"]
```
Собран образ `hello-alpine:local`. Запуск выводит: `Hello from 3.20.7`.

**Артефакты**
- `answers/08_hello_alpine.txt` — вывод контейнера
- `answers/08_history_hello_alpine.txt` — `docker history`
- `answers/08_inspect_hello_alpine.json` — `docker image inspect`

---

## Этап 4 — Docker Compose
Финальный `compose/compose.yaml`:
```yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "8082:80"
    volumes:
      - ../images/site:/usr/share/nginx/html:ro
    healthcheck:
      test: ["CMD-SHELL","wget -qO- http://127.0.0.1 || exit 1"]
    interval: 10s
    timeout: 3s
    retries: 3
    restart: unless-stopped
```

**Артефакты**
- `answers/09_compose_ps.txt` — `docker compose ps`
- `answers/10_compose_page.html` — ответ страницы с `8082`
- `answers/11_compose_logs.txt` — логи сервиса `web`

---

## Этап 5 — Инспекция контейнера из Compose
- Проброс портов: `0.0.0.0:8082 -> 80/tcp`
- Bind‑mount:
  ```
  /srv/devops-netology/05-virt-03-docker-intro/images/site -> /usr/share/nginx/html
  ```
- Сеть: `compose_default`, IP контейнера: `172.18.0.2`

**Артефакты**
- `answers/12_images.txt` — список образов
- `answers/13_inspect_compose_web.json` — `docker inspect` контейнера
- `answers/13a_mounts.txt` — точки монтирования
- `answers/13b_network_ip.txt` — IP/сеть
- `answers/14_history_nginx.txt` — история слоёв `nginx:alpine`

---

## Этап 6 — Save/Load образа и Export контейнера
### Save/Load (image)
- Файл: `answers/hello-alpine.tar` *(не коммитится; добавлен в .gitignore)*
- SHA256: `4659b519eddf739837f8457a48987786e5a75e0fd5bb4eaead1acd2059d091e2`
- Размер: `10M`
- После `docker load` образ запускается и выводит `Hello from 3.20.7`.

### Export (container rootfs)
- Файл: `answers/compose-web-rootfs.tar` *(также игнорируется)*
- SHA256: `468d928da9b257077dae088b2aa2f84797c2ad72ef1fe87d3db1bbdac9f1285a`
- Размер: `54M`

> **Разница:** `docker save/load` работает с **образами** (слои, теги, манифесты), 
> а `docker export` — с **файловой системой контейнера** (без истории, ENV, CMD, ENTRYPOINT и т.п.).

**Артефакты**
- `answers/15_hello_alpine_tar_sha256.txt`
- `answers/15a_hello_alpine_tar_size.txt`
- `answers/16_hello_alpine_after_load.txt`
- `answers/17_export_tar_sha256.txt`
- `answers/17a_export_tar_size.txt`

---

## Быстрые команды
```bash
# Запуск/перезапуск стека
cd 05-virt-03-docker-intro/compose && docker compose up -d

# Проверка
curl -I http://127.0.0.1:8082
docker compose ps
docker compose logs -n 50

# Остановка
docker compose down
```

## Заметки
- В каталоге задания добавлен `.gitignore`, исключающий крупные артефакты (`*.tar`, `*.tgz`).
- Все текстовые выводы сохранены в `answers/` и закоммичены в ветку `05-virt-03-docker-intro`.

---

**Commits по этапам:**
- Этап 4: `b1c684f` → правки compose, далее `b0a8ab4` (.gitignore)
- Этап 5: `52e35e9`
- Этап 6: `235c31a`
