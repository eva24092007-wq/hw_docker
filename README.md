# Д/З по Docker



## 4 балла – один контейнер

Написала Dockerfile, собрала образ и запустила приложение. Опубликовала порт, передала приветствие через переменную окружения и проверила увеличение счётчика.



**Команды запуска:**

```bash
docker build -t counter-app .
docker run -d --name my\_counter -p 8080:8000 -e GREETING="Hello!" counter-app
```



**Логи:**

```
2026-11-24 17:35:25,512 listening port=8000 storage=sqlite
2026-11-24 17:35:56,714 http 172.17.0.1 "GET / HTTP/1.1" 200 -
2026-11-24 17:36:17,799 http 172.17.0.1 "POST /increment HTTP/1.1" 200 -
```



![Скриншот 1](images/1.png)



## 6 баллов – Compose и данные



Настроила запуск через Docker Compose. Сделала приветствие и порт, на котором приложение доступно в системе, настраиваемыми, добавила .env.example. Подключила именованный volume. Счётчик сохраняется после удаления и повторного создания контейнера.



**Логи:**

```

app-1  | 2026-11-24 18:02:34,212 listening port=8000 storage=sqlite
app-1  | 2026-11-24 18:02:45,261 http 172.18.0.1 "GET / HTTP/1.1" 200 -

```



![Скриншот 2](images/2.png)



## 8 баллов – отдельное хранилище



В compose.yaml работают два сервиса: app и redis. Проверила работу счётчика.



**Команды:**
```bash
curl.exe -X POST http://localhost:8080/increment
{"greeting": "Hello, it's Compose!", "count": 1, "hostname": "86ce19ba3b25", "storage": "redis"}
curl.exe -X POST http://localhost:8080/increment
{"greeting": "Hello, it's Compose!", "count": 2, "hostname": "86ce19ba3b25", "storage": "redis"}
docker compose down
docker compose up -d
curl.exe http://localhost:8080/
{"greeting": "Hello, it's Compose!", "count": 2, "hostname": "783edd51425e", "storage": "redis"}
```
```bash
docker compose stop redis
curl.exe -i http://localhost:8080/healthz
HTTP/1.0 200 OK
{"status": "alive"}
curl.exe -i http://localhost:8080/readyz
HTTP/1.0 503 Service Unavailable
{"error": "storage_unavailable"}
curl.exe -i -X POST http://localhost:8080/increment
HTTP/1.0 503 Service Unavailable
{"error": "storage_unavailable"}
```
```bash
docker compose start redis
curl.exe http://localhost:8080/
{"greeting": "Hello, it's Compose!", "count": 2, "hostname": "783edd51425e", "storage": "redis"}
curl.exe -X POST http://localhost:8080/increment
{"greeting": "Hello, it's Compose!", "count": 3, "hostname": "783edd51425e", "storage": "redis"}
```
