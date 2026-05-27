# DAS Monorepo - Sistema con Django y Next.js

Monorepo completo para el proyecto DAS (Django System) que incluye frontend y backend.

## 📁 Estructura del Proyecto

```
monorepo/
├── frontend/              # Aplicación Next.js
│   ├── app/              # App router de Next.js
│   ├── components/       # Componentes React
│   ├── Dockerfile        # Docker para frontend
│   └── package.json
├── backend/              # API REST con Django
│   ├── restApi/         # Proyecto Django
│   ├── Dockerfile       # Docker para backend
│   └── pyproject.toml
├── docker-compose.yml   # Orquestación de servicios
└── .env.example         # Variables de entorno

```

## 🚀 Quick Start

### 1. Clonar y configurar

```bash
# Clonar el repositorio
git clone <repo-url>
cd monorepo

# Copiar archivo de variables de entorno
cp .env.example .env
```

### 2. Levantar todos los servicios

```bash
# Levantar toda la infraestructura
docker-compose up -d

# Ver logs
docker-compose logs -f

# Verificar que todo está corriendo
docker-compose ps
```

### 3. Acceder a los servicios

- **Frontend:** http://localhost:3000
- **Backend API:** http://localhost:8000
- **API Docs:** http://localhost:8000/api/docs/
- **Django Admin:** http://localhost:8000/admin

## 🛠️ Servicios

### Frontend (Next.js)
- **Puerto:** 3000
- **Tecnologías:** Next.js 16, React 19, Material-UI, Tailwind CSS
- **Dockerfile:** `frontend/Dockerfile`

### Backend (Django)
- **Puerto:** 8000
- **Tecnologías:** Django 6.0, DRF, JWT Auth, PostgreSQL
- **Dockerfile:** `backend/Dockerfile`

### PostgreSQL
- **Puerto:** 5432
- **Database:** `das_db`
- **Usuario:** `das_user`
- **Password:** `das_password`

## 🔧 Comandos Útiles

### Gestión de Servicios

```bash
# Levantar servicios específicos
docker-compose up -d frontend backend postgres

# Solo backend y base de datos
docker-compose up -d backend postgres

# Ver logs de un servicio específico
docker-compose logs -f backend

# Reiniciar un servicio
docker-compose restart backend

# Detener todo
docker-compose down

# Detener y eliminar volúmenes (¡cuidado! borra la DB)
docker-compose down -v
```

### Backend (Django)

```bash
# Ejecutar migraciones
docker-compose exec backend python restApi/manage.py migrate

# Crear superusuario
docker-compose exec backend python restApi/manage.py createsuperuser

# Acceder al shell de Django
docker-compose exec backend python restApi/manage.py shell

# Ver logs del backend
docker-compose logs -f backend
```

### PostgreSQL

```bash
# Conectarse a la base de datos
docker-compose exec postgres psql -U das_user -d das_db

# Backup de la base de datos
docker-compose exec postgres pg_dump -U das_user das_db > backup.sql

# Restaurar backup
docker-compose exec -T postgres psql -U das_user -d das_db < backup.sql

# Ver logs de PostgreSQL
docker-compose logs -f postgres
```

### Reconstruir Servicios

```bash
# Reconstruir un servicio específico
docker-compose up -d --build backend

# Reconstruir todo
docker-compose up -d --build

# Forzar recreación de contenedores
docker-compose up -d --force-recreate
```

## 🔍 Desarrollo Local (sin Docker)

### Backend

```bash
cd backend

# Instalar dependencias
uv sync

# Activar entorno virtual
source .venv/bin/activate

# Ejecutar migraciones (usa SQLite por defecto)
cd restApi
python manage.py migrate

# Crear superusuario
python manage.py createsuperuser

# Ejecutar servidor
python manage.py runserver
```

### Frontend

```bash
cd frontend

# Instalar dependencias
npm install

# Ejecutar en modo desarrollo
npm run dev

# Build de producción
npm run build
npm start
```

## 🌐 URLs de Desarrollo

### Servicios principales
- **Frontend:** http://localhost:3000
- **Backend API:** http://localhost:8000
- **Django Admin:** http://localhost:8000/admin
- **API Documentation:** http://localhost:8000/api/docs/
- **API Schema:** http://localhost:8000/api/schema/

### Bases de datos
- **PostgreSQL:** localhost:5432

## 🔐 Seguridad

### Para Producción

1. **Cambiar credenciales de PostgreSQL:**
   ```bash
   POSTGRES_PASSWORD=<password-seguro>
   ```

2. **Cambiar SECRET_KEY de Django:**
   ```bash
   DJANGO_SECRET_KEY=<clave-segura-generada>
   ```

3. **Deshabilitar DEBUG:**
   ```bash
   DEBUG=False
   ```

4. **Configurar ALLOWED_HOSTS:**
   ```bash
   ALLOWED_HOSTS=tudominio.com,www.tudominio.com
   ```

5. **Usar HTTPS** en producción

6. **Configurar CORS correctamente** en el backend

## 📊 Monitoreo

### Verificar salud de los servicios

```bash
# Ver estado de todos los servicios
docker-compose ps

# Ver uso de recursos
docker stats

# Healthcheck de backend
curl http://localhost:8000/health/
```

## 🐛 Troubleshooting

### Error de conexión a PostgreSQL

```bash
# Verificar que PostgreSQL está corriendo
docker-compose ps postgres

# Ver logs de PostgreSQL
docker-compose logs postgres

# Verificar healthcheck
docker-compose exec postgres pg_isready -U das_user -d das_db
```

### Puertos en uso

```bash
# Liberar puerto 3000, 8000 o 5432
# En Linux/Mac:
sudo lsof -ti:3000 | xargs kill -9

# En Windows:
netstat -ano | findstr :3000
taskkill /PID <PID> /F
```

### Reconstruir desde cero

```bash
# Detener todo y eliminar volúmenes
docker-compose down -v

# Eliminar imágenes
docker-compose down --rmi all

# Reconstruir y levantar
docker-compose up -d --build
```

## 📝 Variables de Entorno

Copia `.env.example` a `.env` y ajusta según tus necesidades:

```bash
# Base de datos
POSTGRES_DB=das_db
POSTGRES_USER=das_user
POSTGRES_PASSWORD=das_password

# Django
DJANGO_SECRET_KEY=change-this
DEBUG=True

# Frontend
NEXT_PUBLIC_API_URL=http://localhost:8000
```

## 🧪 Testing

### Backend

```bash
# Ejecutar tests
docker-compose exec backend python restApi/manage.py test

# Con coverage
docker-compose exec backend coverage run --source='.' restApi/manage.py test
docker-compose exec backend coverage report
```

### Frontend

```bash
# Ejecutar tests (si están configurados)
docker-compose exec frontend npm test

# Lint
docker-compose exec frontend npm run lint
```

## 📚 Documentación Adicional

- [Frontend README](./frontend/README.md)
- [Backend README](./backend/README.md)
- [Django REST Framework](https://www.django-rest-framework.org/)
- [Next.js Documentation](https://nextjs.org/docs)

## 🤝 Contribuir

1. Crea una rama: `git checkout -b feature/nueva-funcionalidad`
2. Commit tus cambios: `git commit -am 'Añadir nueva funcionalidad'`
3. Push a la rama: `git push origin feature/nueva-funcionalidad`
4. Crea un Pull Request

## 📄 Licencia

Este proyecto es parte del curso DAS de Comillas.

---

## 🎯 Próximos Pasos

Después de levantar los servicios:

1. ✅ Crear superusuario en Django
2. ✅ Probar la API desde http://localhost:8000/api/docs/
3. ✅ Verificar el frontend en http://localhost:3000
4. ✅ Integrar la comunicación frontend-backend
