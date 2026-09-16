University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Введение в веб технологии](https://itmo-ict-faculty.github.io/introduction-in-web-tech/)
Year: 2026
Group: U4225
Author: Kudelin Dmitry
Lab: Lab0
Date of create: 16.09.2026
Date of finished: 16.09.2026

# Отчёт по лабораторной работе №0
## "Создание репозитория и настройка рабочего окружения"

## Цель работы
Научиться создавать репозитории, настраивать рабочее окружение и изучать основы работы с Git и GitHub.

## Ход работы

### 1. Настройка SSH ключей
- Сгенерирован SSH ключ командой `ssh-keygen -t ed25519`
- Публичный ключ добавлен в настройки GitHub

### 2. Создание репозитория
- Создан репозиторий **devops-lab-kudelin**
- Добавлен README.md при создании

### 3. Клонирование
- Репозиторий склонирован через SSH с использованием GitHub Codespaces

### 4. Создание файлов
- **README.md** — описание проекта и план изучения DevOps
- **.gitignore** — исключения для Git
- **CONTRIBUTING.md** — правила участия в проекте

### 5. Работа с ветками
- Создана ветка `develop`: `git checkout -b develop`

### 6. Коммит и отправка
```bash
git add .
git commit -m "Initial project setup"
git push origin develop
