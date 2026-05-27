# Backend — API REST con Django

Este es el backend del proyecto DAS. Es una API REST construida con Django y Django REST Framework (DRF).

## 🧰 Qué tecnologías usa

| Tecnología | Para qué sirve |
|---|---|
| **Django 6** | Framework web de Python |
| **Django REST Framework** | Herramientas para construir APIs REST |
| **SimpleJWT** | Autenticación con tokens JWT |
| **drf-spectacular** | Genera documentación Swagger automáticamente |
| **uv** | Gestor de dependencias Python (más rápido que pip) |
| **SQLite** | Base de datos para desarrollo local (sin instalar nada) |
| **PostgreSQL** | Base de datos cuando se despliega con Docker |

---

## 🚀 Puesta en marcha en local

### Prerrequisitos

- Python 3.14+
- [uv](https://docs.astral.sh/uv/getting-started/installation/)

```bash
python --version   # debe ser 3.14+
uv --version       # cualquier versión
```

---

### Paso 1 — Instalar dependencias

Desde la carpeta `backend/`:

```bash
uv sync
```

Esto crea un entorno virtual en `.venv/` e instala todas las librerías de `pyproject.toml`. Solo necesitas ejecutarlo una vez (o cuando cambien las dependencias).

> 💡 No necesitas activar el entorno virtual. Con `uv run` puedes ejecutar cualquier comando directamente y uv se encarga de usarlo de forma transparente.

---

### Paso 2 — Crear la base de datos

En local usamos **SQLite**: un fichero `.sqlite3` que Django gestiona solo. No necesitas instalar ningún servidor de base de datos.

Ejecuta las migraciones para crear las tablas:

```bash
cd restApi
uv run python manage.py migrate
```

Esto crea el archivo `restApi/db.sqlite3`. Si lo borras accidentalmente, solo tienes que ejecutar `migrate` de nuevo.

---

### Paso 3 — Arrancar el servidor

```bash
uv run python manage.py runserver
```

El servidor estará disponible en **http://localhost:8000**.

El servidor de desarrollo **se reinicia solo** cuando guardas cambios en el código. No necesitas pararlo y volver a arrancarlo.

---

## 📁 Estructura del proyecto

```
backend/
├── restApi/                    # Raíz del proyecto Django
│   ├── restApi/                # Paquete de configuración principal
│   │   ├── settings.py         # Configuración global (apps, base de datos, JWT...)
│   │   ├── urls.py             # Rutas URL raíz del proyecto
│   │   └── wsgi.py             # Punto de entrada para servidores web
│   ├── healthcheck/            # App de ejemplo incluida en el proyecto
│   │   ├── views.py            # Lógica de la vista
│   │   ├── urls.py             # Rutas de esta app
│   │   └── models.py           # Modelos de base de datos (vacío en este caso)
│   ├── db.sqlite3              # Base de datos SQLite (solo en local)
│   └── manage.py               # CLI de Django
├── pyproject.toml              # Lista de dependencias del proyecto
├── uv.lock                     # Versiones exactas de dependencias (no tocar)
├── .python-version             # Versión de Python que usa el proyecto
└── Dockerfile                  # Para despliegue con Docker (no para desarrollo)
```

> 📌 En Django, el código se organiza en **apps**. Cada app agrupa los modelos, vistas y URLs de una funcionalidad concreta. La app `healthcheck` es un ejemplo sencillo ya incluido.

---

## 📡 Endpoints disponibles

Con el servidor arrancado, tienes acceso a:

| URL | Descripción |
|---|---|
| http://localhost:8000/api/healthcheck/ | Comprueba que la API responde |
| http://localhost:8000/api/docs | Documentación interactiva (Swagger UI) |
| http://localhost:8000/api/schema/ | Schema OpenAPI en JSON |
| http://localhost:8000/admin | Panel de administración de Django |

La **documentación Swagger** (`/api/docs`) es muy útil: lista todos los endpoints disponibles y permite probarlos directamente desde el navegador, sin necesitar Postman ni similar.

---

## 🗄️ Base de datos

### En local: SQLite (sin configuración)

Por defecto Django usa SQLite. No necesitas instalar nada, el archivo `db.sqlite3` se crea automáticamente. Es perfecto para desarrollo.

### En Docker: PostgreSQL

Cuando arrancas con `docker-compose up`, el backend se conecta automáticamente a un contenedor de PostgreSQL. Esto lo controla la variable de entorno `USE_POSTGRES=true`, definida en `docker-compose.yml`.

La lógica está en `settings.py`:

```python
if os.getenv("USE_POSTGRES", "false").lower() == "true":
    # Configuración para PostgreSQL (Docker)
    DATABASES = { "default": { "ENGINE": "django.db.backends.postgresql", ... } }
else:
    # Configuración para SQLite (local)
    DATABASES = { "default": { "ENGINE": "django.db.backends.sqlite3", ... } }
```

No necesitas tocar nada: en local siempre usa SQLite, en Docker siempre usa PostgreSQL.

---

## ⚙️ Variables de entorno

### La regla de oro: `.env.example` vs `.env`

| Fichero | ¿Se sube al repo? | Para qué sirve |
|---|---|---|
| `.env.example` | ✅ Sí | Plantilla con todas las variables disponibles (sin valores reales) |
| `.env` | ❌ No (está en `.gitignore`) | Tu configuración local con los valores reales |

El flujo es siempre el mismo: copias `.env.example` → `.env`, rellenas tus valores, y Django lo carga automáticamente al arrancar.

---

### ¿Cuándo necesitas crear un `.env`?

**En desarrollo local normal: nunca.** Django usa SQLite por defecto y arranca sin ninguna configuración extra. No necesitas ningún fichero `.env`.

Solo necesitas crear un `.env` si quieres conectar Django a un **PostgreSQL local** (en vez de SQLite):

```bash
# Desde la raíz del monorepo
cp backend/.env.example backend/.env
```

Luego edita `backend/.env` y cambia las variables que necesites:

```bash
USE_POSTGRES=true
POSTGRES_HOST=localhost   # tu PostgreSQL local
POSTGRES_DB=das_db
POSTGRES_USER=das_user
POSTGRES_PASSWORD=tu_password
```

---

### Variables disponibles

| Variable | Valor por defecto | Descripción |
|---|---|---|
| `USE_POSTGRES` | `false` | `true` para PostgreSQL, `false` para SQLite |
| `POSTGRES_DB` | `das_db` | Nombre de la base de datos |
| `POSTGRES_USER` | `das_user` | Usuario de PostgreSQL |
| `POSTGRES_PASSWORD` | `das_password` | Contraseña de PostgreSQL |
| `POSTGRES_HOST` | `postgres` | Host de PostgreSQL (`localhost` en local, `postgres` en Docker) |
| `POSTGRES_PORT` | `5432` | Puerto de PostgreSQL |

> 💡 Todos los valores tienen un **default razonable**, por eso funciona sin `.env`. El fichero solo es necesario cuando quieres sobreescribir esos defaults.

---

## 🔧 Comandos de referencia rápida

### Dependencias con uv

```bash
# Instalar todas las dependencias (primera vez o tras cambios en pyproject.toml)
uv sync

# Añadir una nueva librería
uv add nombre-libreria

# Eliminar una librería
uv remove nombre-libreria
```

### Base de datos (desde `restApi/`)

```bash
# Tras modificar un models.py, genera el fichero de migración
uv run python manage.py makemigrations

# Aplica las migraciones pendientes a la base de datos
uv run python manage.py migrate

# Muestra el estado de todas las migraciones
uv run python manage.py showmigrations
```

### Superusuario

```bash
# Crea un usuario administrador para acceder a /admin
uv run python manage.py createsuperuser
```

### Consola interactiva

```bash
# Abre una consola Python con todo el contexto de Django cargado
# Útil para probar modelos y queries directamente
uv run python manage.py shell
```

---

## ➕ Crear una nueva app Django

Cuando quieras añadir una nueva funcionalidad, crea una app nueva:

```bash
# Desde la carpeta restApi/
uv run python manage.py startapp nombre_app
```

Después necesitas hacer tres cosas:

**1. Registrar la app en `settings.py`:**
```python
INSTALLED_APPS = [
    ...
    'nombre_app',   # añade esta línea
]
```

**2. Crear las URLs de la app en `nombre_app/urls.py`:**
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.mi_vista, name='mi-vista'),
]
```

**3. Incluir esas URLs en `restApi/urls.py`:**
```python
from django.urls import include, path

urlpatterns = [
    ...
    path('api/nombre_app/', include('nombre_app.urls')),
]
```

---

## 🐳 Despliegue con Docker

El backend forma parte del `docker-compose.yml` de la raíz del monorepo. Para arrancarlo junto con el frontend y la base de datos, consulta la [guía de Docker en el README raíz](../README.md#-despliegue-con-docker-compose).

```bash
# Desde la raíz del monorepo
docker-compose up -d
```

Si solo quieres arrancar el backend y la base de datos (sin el frontend):

```bash
docker-compose up -d backend postgres
```
