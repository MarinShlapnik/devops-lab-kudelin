University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Введение в веб технологии](https://itmo-ict-faculty.github.io/introduction-in-web-tech/)
Year: 2026
Group: U4225
Author: Kudelin Dmitry
Lab: Lab1
Date of create: 16.09.2026
Date of finished: 16.09.2026

# Отчёт по лабораторной работе №2
## "CI/CD для Docker приложения"

## Цель работы
Научиться настраивать автоматизированные пайплайны для сборки Docker образов, их публикации в registry и автоматического деплоя при изменении кода.

---

## Ход работы

### 1. Подготовка проекта

Файлы из первой лабораторной работы (app.py, requirements.txt, Dockerfile) скопированы в новый репозиторий `cicd-lab-kudelin`.

Создан аккаунт на Docker Hub (username: herrmarin) и публичный репозиторий `my-flask-app`.

### 2. Настройка GitHub Actions

Создан файл `.github/workflows/docker-build.yml` со следующим пайплайном:

```yaml
name: Docker CI/CD

on:
  push:
    branches:
      - main
      - develop

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ secrets.DOCKER_USERNAME }}/my-flask-app:latest

      - name: Deploy step
        run: |
          if [ "${{ github.ref }}" == "refs/heads/main" ]; then
            echo "Deploying to production server..."
          elif [ "${{ github.ref }}" == "refs/heads/develop" ]; then
            echo "Deploying to development server..."
          fi





# Пайплайн запускается при пуше в ветки main и develop, использует Ubuntu runner, выполняет checkout кода, 
# настраивает Docker Buildx, логинится в Docker Hub через секреты, собирает и пушит образ с тегом latest, 
# а также выводит сообщение о деплое в зависимости от ветки.

## 3. Настройка секретов

# В Settings → Secrets and variables → Actions добавлены два секрета:
# DOCKER_USERNAME - логин на Docker Hub (herrmarin)
# DOCKER_PASSWORD - токен доступа Docker Hub

## 4. Тестирование пайплайна

# После пуша в ветку main пайплайн успешно выполнился:
![Выполнение пайплайна](build_and_push.png)

## 5. Проверка Docker Hub

![Репозиторий на Docker Hub](Docker_hub_latests.png)

## ## Выводы
В ходе лабораторной работы я научился настраивать CI/CD пайплайны с помощью GitHub Actions, автоматически собирать и публиковать Docker образы в Docker Hub. Использование секретов позволяет безопасно хранить учётные данные без их публикации в коде. Пайплайн успешно выполняется при каждом пуше в ветку main, собирает Docker образ и загружает его в Docker Hub с тегом latest.

