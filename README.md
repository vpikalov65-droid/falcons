# Falcons

Ретро-шутер в одном файле: HTML, CSS и JavaScript, без серверной логики и внешних ресурсов. Звук собирается через Web Audio API после первого нажатия.

## Запуск локально

Самый простой способ — открыть `index.html` в браузере.

Через Docker:

```bash
docker compose up --build -d
```

Игра будет на [http://127.0.0.1:8088/](http://127.0.0.1:8088/). Остановка: `docker compose down`.

## Как играть

- Стрелки влево и вправо — движение бластера
- Пробел — выстрел
- Рекорд сохраняется в браузере (`localStorage`)

## Деплой

На сервере с Docker и уже работающим nginx:

```bash
git clone https://github.com/pex2601-oss/falcons.git /srv/falcons
cd /srv/falcons
docker compose up --build -d
```

Контейнер слушает только `127.0.0.1:8088`. Снаружи его открывает хостовый nginx по пути `/falcons/`, например `https://pikalov.ru/falcons/`.

В конфиг сайта добавьте локацию (перед `location /`), затем проверьте и перезагрузите nginx:

```nginx
location = /falcons {
    return 301 /falcons/;
}

location /falcons/ {
    proxy_pass http://127.0.0.1:8088/;
    proxy_http_version 1.1;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    add_header Strict-Transport-Security "max-age=31536000" always;
}
```

```bash
sudo nginx -t && sudo systemctl reload nginx
```
