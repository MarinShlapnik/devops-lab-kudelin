# Отчёт по лабораторной работе №1
 
**"Основы работы с Docker"**
 
---
 
## Информация о работе
 
| Параметр | Значение |
|----------|----------|
| **Университет** | [ITMO University](https://itmo.ru/ru/) |
| **Факультет** | [FICT](https://fict.itmo.ru) |
| **Курс** | [Введение в веб технологии](https://itmo-ict-faculty.github.io/introduction-in-web-tech/) |
| **Год** | 2026 |
| **Группа** | U4225 |
| **Автор** | Kudelin Dmitry |
| **Лабораторная работа** | Lab1 |
| **Дата создания** | 16.09.2026 |
| **Дата завершения** | 16.09.2026 |
 
---
 
## Цель работы
 
Научиться работать с Docker: создавать Dockerfile, собирать образы, запускать контейнеры и управлять ими.
 
> **Примечание:** Работа выполнялась в среде GitHub Codespaces, так как локальная установка Docker Desktop на рабочем ноутбуке невозможна из-за ограничений прав администратора.
 
---
 
## Ход работы
 
### 1. Изучение основ Docker
 
Проверка установки и запуск тестового контейнера для ознакомления с базовыми командами Docker:
 
```bash
docker --version
docker run hello-world
```
 
**Результат:** Docker успешно установлен, тестовый контейнер hello-world выполнен без ошибок.
 
![Docker version и hello-world](../../screen1_docker_version.png)
 
---
 
### 2. Работа с готовыми образами
 
Скачивание образа Ubuntu и установка утилиты curl внутри контейнера:
 
```bash
docker pull ubuntu:latest
docker run -it ubuntu bash
apt update && apt install -y curl
curl --version
```
 
**Результат:** Ubuntu образ успешно скачан, curl установлен и работает корректно внутри контейнера.
 
![Проверка версии curl в Ubuntu контейнере](../../screen2_curl_version.png)
 
---
 
### 3. Запуск веб-сервера Nginx
 
Создание и запуск контейнера с веб-сервером Nginx на основе Alpine Linux:
 
```bash
docker run -d -p 8080:80 --name web-server nginx:alpine
```
 
**Параметры команды:**
- `-d` — запуск в фоновом режиме (detached)
- `-p 8080:80` — проксирование портов (локальный 8080 → контейнер 80)
- `--name web-server` — присвоение имени контейнеру
- `nginx:alpine` — использование легкой версии на базе Alpine Linux
**Результат:** Nginx успешно запущен и доступен по адресу `http://localhost:8080`.
 
![Nginx в браузере](../../screen3_nginx_browser.png)
 
---
 
### 4. Работа с томами (Volumes)
 
Создание и использование Docker тома для персистентного хранения данных:
 
```bash
# Создание тома
docker volume create my-volume
 
# Запуск контейнера с подключенным томом
docker run -it --name volume-test -d -v my-volume:/data ubuntu bash
 
# Создание тестового файла
docker exec -it volume-test bash
echo "Hello from volume" > /data/test.txt
exit
 
# Удаление контейнера (том остается)
docker rm -f volume-test
 
# Запуск нового контейнера с тем же томом
docker run -it --name volume-test-2 -d -v my-volume:/data ubuntu bash
 
# Проверка что данные сохранились
docker exec -it volume-test-2 bash
cat /data/test.txt
```
 
**Результат:** Данные успешно сохранились в томе и доступны для нового контейнера. Это демонстрирует правильную работу персистентного хранилища.
 
![Тестирование томов Docker](../../screen4_volume_test.png)
 
---
 
### 5. Лабораторная работа со звёздочкой (Dockerfile)
 
#### 5.1 Созданные файлы
 
**app.py** — Flask приложение:
 
```python
from flask import Flask
app = Flask(__name__)
 
@app.route('/')
def hello():
    return "Hello from Docker!"
 
if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```
 
**requirements.txt** — зависимости Python:
 
```
Flask==2.0.1
Werkzeug==2.0.3
```
 
**Dockerfile** — инструкции для сборки образа:
 
```dockerfile
FROM python:3.9-slim
WORKDIR /app
RUN apt-get update && apt-get install -y curl vim
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY app.py .
RUN useradd -m -u 1000 appuser && chown -R appuser:appuser /app
USER appuser
EXPOSE 5000
ENV FLASK_ENV=production
CMD ["python", "app.py"]
```
 
**Описание Dockerfile:**
1. `FROM python:3.9-slim` — базовый образ Python 3.9 (минимальный размер)
2. `WORKDIR /app` — рабочая директория внутри контейнера
3. `RUN apt-get update && apt-get install -y curl vim` — установка утилит
4. `COPY requirements.txt .` — копирование зависимостей
5. `RUN pip install -r requirements.txt` — установка Python пакетов
6. `COPY app.py .` — копирование приложения
7. `RUN useradd ... && chown ...` — создание непривилегированного пользователя (лучшая практика безопасности)
8. `USER appuser` — переключение на непривилегированного пользователя
9. `EXPOSE 5000` — указание открытого порта (информационное)
10. `ENV FLASK_ENV=production` — установка переменной окружения
11. `CMD ["python", "app.py"]` — команда запуска приложения
#### 5.2 Сборка образа
 
```bash
docker build -t my-flask-app .
```
 
**Результат:** Образ `my-flask-app` успешно собран со всеми слоями.
 
![Сборка Docker образа](../../screen5_docker_build.png)
 
#### 5.3 Запуск контейнера
 
```bash
docker run -d -p 5000:5000 --name flask-container my-flask-app
docker ps
curl http://localhost:5000
```
 
**Результат:** Flask приложение успешно запущено в контейнере и отвечает на HTTP запросы.
 
![Запущенный Flask контейнер](../../screen6_flask_container.png)
 
 
---
 
**Репозиторий:** [devops-lab-kudelin](https://github.com/your-username/devops-lab-kudelin)





