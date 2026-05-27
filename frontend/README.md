# Frontend — Aplicación web con Next.js

Este es el frontend del proyecto DAS. Es una aplicación web construida con Next.js 16 y React 19.

## 🧰 Qué tecnologías usa

| Tecnología | Para qué sirve |
|---|---|
| **Next.js 16** | Framework de React con enrutamiento, SSR y herramientas de build |
| **React 19** | Librería para construir interfaces de usuario con componentes |
| **npm** | Gestor de paquetes de Node.js |

---

## 🚀 Puesta en marcha en local

### Prerrequisitos

- Node.js 20+

```bash
node --version   # debe ser v20.x o superior
npm --version    # viene incluido con Node.js
```

---

### Paso 1 — Instalar dependencias

Desde la carpeta `frontend/`:

```bash
npm install
```

Esto crea la carpeta `node_modules/` con todas las librerías. Solo necesitas ejecutarlo una vez (o cuando cambien las dependencias en `package.json`).

---

### Paso 2 — Configurar las variables de entorno

```bash
# Desde la carpeta frontend/
cp .env.example .env.local
```

Esto crea tu fichero de configuración local. Ábrelo y comprueba que la URL apunta a tu backend:

```bash
NEXT_PUBLIC_API_URL=http://localhost:8000
```

> 💡 `.env.local` no se sube al repositorio (está en `.gitignore`). Cada desarrollador tiene el suyo propio.

---

### Paso 3 — Arrancar el servidor de desarrollo

```bash
npm run dev
```

La aplicación estará disponible en **http://localhost:3000**.

El servidor de desarrollo tiene **hot reload**: cualquier cambio que guardes en el código se refleja en el navegador automáticamente, sin necesidad de reiniciarlo.

---

## 📁 Estructura del proyecto

```
frontend/
├── src/
│   └── app/                    # Sistema de rutas de Next.js (App Router)
│       ├── layout.js           # Layout global que envuelve todas las páginas
│       ├── page.js             # Página de inicio → ruta "/"
│       └── globals.css         # Estilos CSS globales
├── public/                     # Archivos estáticos (imágenes, iconos, fuentes...)
├── next.config.mjs             # Configuración de Next.js
├── package.json                # Dependencias y scripts disponibles
└── Dockerfile                  # Para despliegue con Docker (no para desarrollo)
```

> 📌 Next.js usa el **App Router**: cada carpeta dentro de `src/app/` se convierte en una ruta de la aplicación. Por ejemplo, si creas `src/app/contacto/page.js`, esa página será accesible en `http://localhost:3000/contacto`.

---

## 🔌 Comunicación con el Backend

Para llamar a la API del backend usa siempre la variable de entorno `NEXT_PUBLIC_API_URL`, **nunca la URL hardcodeada**. Hardcodear URLs es una mala práctica: hace el código frágil y dificulta trabajar en equipo.

```javascript
// ✅ Correcto: usa la variable de entorno
async function verificarApi() {
  const response = await fetch(`${process.env.NEXT_PUBLIC_API_URL}/api/healthcheck/`);
  const data = await response.json();
  console.log(data); // "Greetings from API Chat AI (DAS 2026)"
}
```

```javascript
// ❌ Incorrecto: URL hardcodeada
async function verificarApi() {
  const response = await fetch('http://localhost:8000/api/healthcheck/');
}
```

> 💡 Para que esto funcione, el backend tiene que estar arrancado en local (`uv run python manage.py runserver`).

Puedes explorar todos los endpoints disponibles en la **documentación Swagger** del backend: http://localhost:8000/api/docs

---

## ⚙️ Variables de entorno

### `.env.example` vs `.env.local`

| Fichero | ¿Se sube al repo? | Para qué sirve |
|---|---|---|
| `.env.example` | ✅ Sí | Plantilla con todas las variables (sin valores reales). Se edita en el repo. |
| `.env.local` | ❌ No (está en `.gitignore`) | Tu configuración local con los valores reales. Nunca se comparte. |

El flujo es siempre el mismo: copias `.env.example` → `.env.local` y ajustas tus valores. Next.js carga `.env.local` automáticamente al arrancar con `npm run dev`.

---

### Variables disponibles

| Variable | Valor por defecto | Descripción |
|---|---|---|
| `NEXT_PUBLIC_API_URL` | `http://localhost:8000` | URL base del backend Django |

> ⚠️ Las variables con prefijo `NEXT_PUBLIC_` son **visibles en el navegador** — se incrustan en el JavaScript que se envía al cliente. No pongas contraseñas, tokens privados ni secretos en variables con este prefijo.

---

## 🔧 Comandos de referencia rápida

```bash
# Arrancar en modo desarrollo (con hot reload)
npm run dev

# Comprobar errores de estilo y calidad de código (ESLint)
npm run lint

# Construir la aplicación para producción
npm run build

# Arrancar la build de producción (requiere haber ejecutado npm run build antes)
npm start
```

---

## ➕ Crear una nueva página

1. Crea una carpeta con el nombre de la ruta dentro de `src/app/`
2. Añade un archivo `page.js` dentro de esa carpeta
3. Exporta un componente React por defecto

Ejemplo — crear la página `/sobre-nosotros`:

```javascript
// src/app/sobre-nosotros/page.js

export default function SobreNosotros() {
  return (
    <main>
      <h1>Sobre nosotros</h1>
      <p>Descripción del equipo...</p>
    </main>
  );
}
```

La página estará disponible en http://localhost:3000/sobre-nosotros.

---

## 🐳 Despliegue con Docker

El frontend forma parte del `docker-compose.yml` de la raíz del monorepo. Consulta la [guía de Docker en el README raíz](../README.md#-despliegue-con-docker-compose).

```bash
# Desde la raíz del monorepo
docker-compose up -d
```

> ⚠️ Si modificas el código fuente y quieres verlo reflejado en Docker, necesitas reconstruir la imagen: `docker-compose up -d --build`. Esto es porque el Dockerfile hace un build estático de la app — no hay hot reload en Docker.

---

## 📚 Recursos para aprender

- [Documentación oficial de Next.js](https://nextjs.org/docs)
- [Tutorial interactivo de Next.js](https://nextjs.org/learn) — aprende paso a paso
- [Documentación de React](https://react.dev/) — conceptos base de React
