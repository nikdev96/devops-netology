

# 05‑virt‑03‑docker‑intro — отчёт (macOS)

**Хост:** macOS (Docker Desktop)  
**Цель:** пройти вводные шаги Docker: базовые образы/контейнеры, публикация порта, bind‑mount, свой образ, инспекция слоёв, лимиты ресурсов.

---

## 0) Установка и проверка Docker
```bash
brew install --cask docker
open -a Docker
docker --version
docker info | head -n 20
docker run --rm hello-world
```

---

## 1) Разогрев: образы и контейнеры (Alpine)
```bash
docker pull alpine:3.20
docker images
docker run --rm -it alpine:3.20 sh
# внутри контейнера:
cat /etc/os-release
exit
```

**Выдержка вывода `/etc/os-release`:**
```
NAME="Alpine Linux"
VERSION_ID=3.20.7
PRETTY_NAME="Alpine Linux v3.20"
```

---

## 2) Nginx с публикацией порта (web1)
```bash
docker run -d --name web1 -p 8080:80 nginx:alpine
curl -I http://localhost:8080
docker logs web1 --tail 20
```

**Выдержка `curl -I`:**
```
HTTP/1.1 200 OK
Server: nginx/1.29.0
Content-Type: text/html
```

---

## 3) Bind‑mount статического контента (web2)
Подготовили страницу и смонтировали её в контейнер (только для чтения).

```bash
mkdir -p ~/docker/nginx/html
printf '<h1>Hi Netology</h1>\n' > ~/docker/nginx/html/index.html

docker run -d --name web2 -p 8081:80 \
  -v ~/docker/nginx/html:/usr/share/nginx/html:ro \
  nginx:alpine

curl http://localhost:8081
```

**Ожидаемый ответ:**
```
<h1>Hi Netology</h1>
```

> На macOS bind‑mount работает через VM Docker Desktop, поэтому использовать пути внутри `$HOME` — надёжнее.

---

## 4) Свой образ на базе Nginx (web3)
Dockerfile копирует собственный `index.html` в стандартный корневой каталог Nginx.

```Dockerfile
# Dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

Сборка и запуск:
```bash
mkdir -p ~/docker/mynginx && cd ~/docker/mynginx
printf '<h1>Custom image</h1>\n' > index.html
cat > Dockerfile <<'EOF'
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EOF

docker build -t mynginx:v1 .
docker run -d --name web3 -p 8082:80 mynginx:v1
curl http://localhost:8082
```

**Ожидаемый ответ:**
```
<h1>Custom image</h1>
```

---

## 5) Инспекция слоёв и метаданных
```bash
# История слоёв образа
docker history mynginx:v1

# Ключевая мета‑информация образа
docker inspect mynginx:v1 | jq '.[0].Architecture, .[0].Os'

# Конфигурация контейнера web3
docker inspect web3 | jq '.[0].HostConfig.Memory, .[0].Config.Env'
```

**Выдержки:**
```
# history (верхушка):
COPY index.html /usr/share/nginx/html/index.html   22B
...
# inspect image:
"arm64"
"linux"
# inspect container:
0
["PATH=...","NGINX_VERSION=1.29.0","PKG_RELEASE=1", ...]
```

---

## 6) Ограничение ресурсов контейнера (по желанию)
```bash
docker run --rm -it --name testlim \
  --cpus=1 --memory=256m alpine:3.20 sh
# проверка, затем exit
```

---

## 7) Очистка (по желанию)
```bash
docker rm -f web1 web2 web3 2>/dev/null || true
docker image prune -f
```

---

## Примечания для macOS (Apple Silicon)
- Бóльшая часть официальных образов уже имеет сборки для `linux/arm64`.  
- Если нужен строго `amd64`, можно указать платформу при сборке/запуске:
  ```bash
  docker buildx build --platform linux/amd64 -t mynginx:v1-amd64 . --load
  # или
  docker run --platform linux/amd64 ...
  ```

---

## Краткий чек‑лист выполнения
- [x] Docker установлен, `hello-world` отработал  
- [x] Образ `alpine:3.20` подтянут и запущен (проверен `/etc/os-release`)  
- [x] Контейнер `web1` (nginx) опубликован на `localhost:8080`  
- [x] Контейнер `web2` с bind‑mount (`8081`) отдаёт локальную страницу  
- [x] Собран пользовательский образ `mynginx:v1`, `web3` доступен на `8082`  
- [x] Изучены слои/метаданные (history/inspect)  
- [x] Протестировано ограничение ресурсов контейнера

---
