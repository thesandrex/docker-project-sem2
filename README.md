# 🐳 Контейнеризация Frontend и Backend-приложений

Проект включает два компонента:

- **Frontend** — приложение на Vue.js, работает на порту **80**
- **Backend** — приложение на Go, работает на порту **8081**

Оба компонента упакованы в Docker-контейнеры, оптимизированы и готовы к запуску с помощью `docker-compose`.

---

## 🚀 Быстрый запуск

Для запуска всего проекта выполните:

```bash
docker-compose up -d
```

- Frontend будет доступен по адресу: [http://localhost](http://localhost)
- Backend будет доступен по адресу: [http://localhost:8081](http://localhost:8081)

Для остановки и удаления контейнеров:

```bash
docker-compose down
```

---

## 🛠 Ручная сборка образов

Если не используете образы из Docker Hub, можно собрать их вручную:

### 🔧 Сборка frontend

```bash
cd frontend
docker build -t docker-frontend-app:latest .
```

### 🔧 Сборка backend

```bash
cd backend
docker build -t docker-backend-app:latest .
```

---

## 📦 Оптимизация размеров Docker-образов

### ✅ Использованы multi-stage сборки

- **Frontend**: базовая сборка на `node:20-alpine`, запуск — на `nginx:1.25-alpine`
- **Backend**: сборка на `golang:1.22-alpine`, запуск — на `alpine:latest`

Multi-stage подход позволил исключить dev-зависимости и получить лёгкие образы.

### 📊 Сравнение размеров итоговых образов

| Компонент | Финальный образ           | Размер      | Базовый рантайм     |
|-----------|----------------------------|-------------|----------------------|
| Frontend  | `docker-frontend-app`      | ~28 MB      | `nginx:1.25-alpine`  |
| Backend   | `docker-backend-app`       | ~16 MB      | `alpine:latest`      |

---

## 🔐 Безопасность и надёжность

- **Непривилегированный пользователь**: оба контейнера запускаются от имени `appuser`, созданного в Dockerfile
- **Ограничения ресурсов** заданы в `docker-compose.yml`:
  - `cpus: 0.5`
  - `memory: 256M`
- **security_opt: no-new-privileges** — запрещает повышать привилегии внутри контейнеров
- **healthcheck**: оба контейнера имеют встроенные механизмы проверки готовности:
  - frontend: `curl -fIs -X GET http://localhost`
  - backend: `curl -fIs -X GET http://localhost:8081/health`

---

## 🧾 Структура проекта

```
.
├── docker-compose.yml
├── frontend/
│   ├── Dockerfile
│   ├── package.json
│   ├── public/
│   └── src/
├── backend/
│   ├── Dockerfile
│   ├── go.mod
│   ├── go.sum
│   └── cmd/
└── README.md
```
