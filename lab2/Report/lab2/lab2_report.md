# Отчёт по лабораторной работе №2

**"CI/CD для Docker приложения"**

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

Научиться настраивать автоматизированные пайплайны для сборки Docker образов, их публикации в registry и автоматического деплоя при изменении кода.

---

## Ход работы

### 1. Подготовка проекта

Файлы из первой лабораторной работы (`app.py`, `requirements.txt`, `Dockerfile`) скопированы в новый репозиторий `cicd-lab-kudelin`.

Создан аккаунт на Docker Hub:
- **Username:** `herrmarin`
- **Репозиторий:** `my-flask-app` (публичный)

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
```

**Описание пайплайна:**
- Запускается при пуше в ветки `main` и `develop`
- Использует Ubuntu runner
- Выполняет checkout кода
- Настраивает Docker Buildx для кроссплатформенной сборки
- Логинится в Docker Hub через секреты
- Собирает и пушит образ с тегом `latest`
- Выводит сообщение о деплое в зависимости от ветки

### 3. Настройка секретов

В `Settings → Secrets and variables → Actions` добавлены два секрета:

| Секрет | Значение |
|--------|----------|
| `DOCKER_USERNAME` | `herrmarin` |
| `DOCKER_PASSWORD` | Токен доступа Docker Hub |

### 4. Тестирование пайплайна

После пуша в ветку `main` пайплайн успешно выполнился:

![Выполнение пайплайна](build_and_push.png)


### 5. Проверка Docker Hub

Образ успешно появился в репозитории на Docker Hub:

![Репозиторий на Docker Hub](Docker_hub_latests.png)

---

## Выводы

В ходе выполнения лабораторной работы:

1. Настроен автоматизированный CI/CD пайплайн с использованием GitHub Actions
2. Реализована автоматическая сборка Docker образов при изменении кода
3. Настроена публикация образов в Docker Hub registry
4. Реализована условная логика деплоя для различных веток (main/develop)
5. Использованы GitHub Secrets для безопасного хранения учетных данных

Пайплайн полностью функционален и готов к использованию в production среде.

---

**Репозиторий:** [cicd-lab-kudelin](https://github.com/your-username/cicd-lab-kudelin)  
**Docker Hub:** [herrmarin/my-flask-app](https://hub.docker.com/r/herrmarin/my-flask-app)
