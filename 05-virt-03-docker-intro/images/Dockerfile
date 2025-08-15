FROM alpine:3.20
# добавим bash для удобства
RUN apk add --no-cache bash
# простой стартовый скрипт — выведем версию дистрибутива
CMD ["bash","-lc","echo Hello from $(cat /etc/alpine-release)"]
