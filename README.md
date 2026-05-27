# DAS - Proyecto Full Stack (Django + Next.js)

Monorepo del proyecto de la asignatura **DAS** de Comillas ICAI.

## ¿Qué hay aquí?

```
monorepo/
├── backend/            # API REST con Django (Python)
│   └── restApi/        # Proyecto Django
├── frontend/           # Aplicación web con Next.js (React)
├── docker-compose.yml  # Despliegue completo con Docker
└── README.md           # Este archivo
```

La arquitectura es la habitual en aplicaciones web modernas:

```
Navegador  ──►  Frontend (Next.js :3000)  ──►  Backend (Django :8000)  ──►  Base de datos
```

---

## 🛠️ Cómo trabajar en este proyecto

Hay **dos modos** de trabajo:

### Modo 1 — Desarrollo local (el día a día)

Arrancas el backend y el frontend por separado en tu máquina. Es el modo habitual cuando estás escribiendo código.

- 👉 [Guía del Backend (Django + uv)](./backend/README.md)
- 👉 [Guía del Frontend (Next.js + npm)](./frontend/README.md)

### Modo 2 — Despliegue con Docker Compose

Cuando quieras ver todo el stack funcionando junto (backend + frontend + PostgreSQL) de un solo comando. Útil para probar la integración completa o hacer una demo.

- 👉 [Guía de Docker](#-despliegue-con-docker-compose) (más abajo en este mismo archivo)

---

## ✅ Prerrequisitos

Instala estas herramientas antes de empezar:

| Herramienta | Versión mínima | Para qué |
|---|---|---|
| [Python](https://www.python.org/downloads/) | 3.14+ | Ejecutar Django |
| [uv](https://docs.astral.sh/uv/getting-started/installation/) | cualquiera | Gestionar dependencias Python |
| [Node.js](https://nodejs.org/) | 20 LTS | Ejecutar Next.js |
| [Docker Desktop](https://www.docker.com/products/docker-desktop/) | cualquiera | Solo para el modo Docker |

Comprueba que están correctamente instalados:

```bash
python --version      # Python 3.14.x
uv --version          # uv x.x.x
node --version        # v20.x.x
npm --version         # 10.x.x
docker --version      # Docker version xx.x.x
```

---

## ⚙️ Variables de entorno

El proyecto usa ficheros `.env` para configuración que puede cambiar entre máquinas. El esquema es siempre el mismo:

| Fichero | ¿Se sube al repo? | Para qué sirve |
|---|---|---|
| `.env.example` | ✅ Sí | Plantilla con todas las variables (sin valores reales) |
| `.env` / `.env.local` | ❌ No | Tu configuración local con los valores reales |

**En este proyecto, para desarrollo local y Docker no necesitas crear ningún `.env` en la raíz.** Cada servicio tiene sus propios defaults y el `docker-compose.yml` lleva todo configurado.

Los ficheros relevantes están en cada servicio:

| Fichero | Cuándo copiarlo |
|---|---|
| `backend/.env.example` → `backend/.env` | Solo si quieres conectar Django a un PostgreSQL local |
| `frontend/.env.example` → `frontend/.env.local` | Solo si quieres configurar la URL del backend mediante variable de entorno |

> 💡 Si es la primera vez que trabajas en el proyecto, **no necesitas crear ningún `.env`**. Arranca directamente siguiendo las guías de backend y frontend.

---

## 🐳 Despliegue con Docker Compose

> **Esto no es para desarrollar.** Para escribir código, usa el modo de desarrollo local.
>
> Usa Docker Compose cuando quieras probar el despliegue completo.

### Arrancar todo

```bash
# Desde la raíz del monorepo
docker-compose up -d
```

Esto construye las imágenes y arranca tres contenedores:

| Servicio | URL | Descripción |
|---|---|---|
| Frontend | http://localhost:3000 | Aplicación Next.js |
| Backend | http://localhost:8000 | API REST Django |
| Swagger | http://localhost:8000/api/docs | Documentación de la API |
| Admin | http://localhost:8000/admin | Panel de administración Django |
| Postgres | puerto 5432 | Base de datos (solo acceso interno) |

> La primera vez tarda un poco porque Docker construye las imágenes desde cero.

### Primera vez: crear un superusuario de Django

Para poder acceder al panel de administración (`/admin`), necesitas crear un superusuario:

```bash
docker-compose exec backend python restApi/manage.py createsuperuser
```

### Comandos habituales

```bash
# Ver el estado de los servicios
docker-compose ps

# Ver logs en tiempo real (todos los servicios)
docker-compose logs -f

# Ver logs de un servicio concreto
docker-compose logs -f backend
docker-compose logs -f frontend

# Parar todos los servicios
docker-compose down

# Parar y borrar la base de datos (¡los datos se pierden!)
docker-compose down -v
```

### Si cambias el código fuente

Los contenedores se construyen a partir de una imagen fija. Si modificas el código, necesitas reconstruir:

```bash
# Reconstruir y volver a arrancar
docker-compose up -d --build
```

### Solución de problemas

**Error: puerto ya en uso**

Significa que tienes el servidor local (Django o Next.js) arrancado. Para los servicios locales antes de usar Docker:

```bash
# Comprueba qué está usando el puerto
# Linux / Mac
lsof -i :8000
lsof -i :3000

# Windows
netstat -ano | findstr :8000
```

**Los cambios de código no se ven**

```bash
docker-compose up -d --build
```

**Reconstruir desde cero**

```bash
docker-compose down -v
docker-compose up -d --build
```
