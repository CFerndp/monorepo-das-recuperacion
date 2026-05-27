# Backend DAS - Django REST API

Backend service para la aplicación DAS (Django AI System) con soporte para PostgreSQL y SQLite.

## 🚀 Características

- Django 6.0 con Django REST Framework
- Autenticación JWT con refresh tokens
- Soporte para PostgreSQL (Docker) y SQLite (local)
- Documentación automática de API con drf-spectacular
- CORS habilitado para desarrollo

## 📋 Requisitos

- Python 3.14+
- PostgreSQL 16+ (para producción/Docker)
- uv (gestor de paquetes Python)

## 🛠️ Instalación

### Desarrollo Local (SQLite)

```bash
# Instalar dependencias
uv sync

# Activar el entorno virtual
source .venv/bin/activate

# Crear archivo .env (opcional para desarrollo local)
cp .env.example .env

# Ejecutar migraciones
cd restApi
python manage.py migrate

# Crear superusuario
python manage.py createsuperuser

# Ejecutar servidor de desarrollo
python manage.py runserver
```

El servidor estará disponible en `http://localhost:8000`

### Con Docker (PostgreSQL)

```bash
# Desde la raíz del monorepo
docker-compose up -d backend

# Ver logs
docker-compose logs -f backend

# Ejecutar migraciones (si es necesario)
docker-compose exec backend python restApi/manage.py migrate

# Crear superusuario
docker-compose exec backend python restApi/manage.py createsuperuser
```

## 🗄️ Configuración de Base de Datos

### SQLite (Desarrollo Local)

Por defecto, el proyecto usa SQLite para desarrollo local. No requiere configuración adicional.

### PostgreSQL (Docker/Producción)

Para usar PostgreSQL, configura las siguientes variables de entorno:

```bash
USE_POSTGRES=true
POSTGRES_DB=das_db
POSTGRES_USER=das_user
POSTGRES_PASSWORD=das_password
POSTGRES_HOST=postgres  # o localhost si está fuera de Docker
POSTGRES_PORT=5432
```

El servicio de PostgreSQL está configurado en `docker-compose.yml` con:
- **Usuario:** das_user
- **Password:** das_password
- **Base de datos:** das_db
- **Puerto:** 5432

## 🔧 Variables de Entorno

Copia `.env.example` a `.env` y ajusta según tus necesidades:

```bash
# Database
USE_POSTGRES=false                    # true para PostgreSQL, false para SQLite
POSTGRES_DB=das_db
POSTGRES_USER=das_user
POSTGRES_PASSWORD=das_password
POSTGRES_HOST=postgres
POSTGRES_PORT=5432

# Django
SECRET_KEY=your-secret-key-here
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1
```

## 📚 Estructura del Proyecto

```
backend/
├── restApi/               # Proyecto Django principal
│   ├── restApi/          # Configuración del proyecto
│   │   ├── settings.py   # Configuración (incluye DB)
│   │   ├── urls.py       # URLs principales
│   │   └── wsgi.py
│   ├── healthcheck/      # App de health checks
│   └── manage.py         # CLI de Django
├── pyproject.toml        # Dependencias del proyecto
├── Dockerfile            # Configuración Docker
└── .env.example          # Ejemplo de variables de entorno
```

## 🔌 Endpoints Principales

### Health Check

- `GET /health/` - Estado del servicio

### Documentación API

- `GET /api/schema/` - Schema OpenAPI
- `GET /api/docs/` - Documentación interactiva (Swagger UI)

## 🐳 Docker

### Construir imagen

```bash
docker build -t backend-das:latest ./backend
```

### Ejecutar contenedor standalone

```bash
docker run -p 8000:8000 \
  -e USE_POSTGRES=true \
  -e POSTGRES_HOST=postgres \
  -e POSTGRES_DB=das_db \
  -e POSTGRES_USER=das_user \
  -e POSTGRES_PASSWORD=das_password \
  backend-das:latest
```

### Con docker-compose

```bash
# Levantar backend con PostgreSQL
docker-compose up -d backend postgres

# Ver logs
docker-compose logs -f backend

# Acceder al shell de Django
docker-compose exec backend python restApi/manage.py shell

# Ejecutar migraciones
docker-compose exec backend python restApi/manage.py migrate

# Crear superusuario
docker-compose exec backend python restApi/manage.py createsuperuser
```

## 🧪 Testing

```bash
# Ejecutar tests
python restApi/manage.py test

# Con coverage
coverage run --source='.' restApi/manage.py test
coverage report
```

## 🔍 Linting

```bash
# Ejecutar pylint
pylint restApi/
```

## 📊 Migraciones

```bash
# Crear migraciones
python manage.py makemigrations

# Aplicar migraciones
python manage.py migrate

# Ver estado de migraciones
python manage.py showmigrations

# Ver SQL de una migración
python manage.py sqlmigrate app_name migration_name
```

## 🔐 Seguridad

- Cambia `SECRET_KEY` en producción
- Configura `DEBUG=False` en producción
- Actualiza `ALLOWED_HOSTS` con tus dominios
- Usa contraseñas seguras para PostgreSQL
- Implementa HTTPS en producción
- Revisa y ajusta los tiempos de expiración de JWT

## 📝 Comandos Útiles

```bash
# Instalar nueva dependencia
uv add nombre-paquete

# Actualizar dependencias
uv sync

# Ver información de la base de datos
docker-compose exec postgres psql -U das_user -d das_db

# Backup de la base de datos
docker-compose exec postgres pg_dump -U das_user das_db > backup.sql

# Restaurar backup
docker-compose exec -T postgres psql -U das_user -d das_db < backup.sql

# Ver logs de PostgreSQL
docker-compose logs -f postgres
```

## 🌐 URLs de Desarrollo

- **Backend API:** http://localhost:8000
- **Admin Django:** http://localhost:8000/admin
- **API Docs:** http://localhost:8000/api/docs/
- **PostgreSQL:** localhost:5432

## 🤝 Contribuir

1. Crea una rama para tu feature: `git checkout -b feature/nueva-funcionalidad`
2. Commit tus cambios: `git commit -am 'Añadir nueva funcionalidad'`
3. Push a la rama: `git push origin feature/nueva-funcionalidad`
4. Crea un Pull Request

## 📄 Licencia

Este proyecto es parte del curso DAS de Comillas.