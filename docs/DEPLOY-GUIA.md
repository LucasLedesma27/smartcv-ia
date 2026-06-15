# Guía de Deploy — SmartCV IA

> Paso a paso para dejar la app online (Neon + Render + Vercel).  
> Tiempo estimado: **45–60 minutos**.

**Repo:** https://github.com/LucasLedesma27/smartcv-ia

---

## Antes de empezar

Tener a mano:
- [ ] Cuenta GitHub (ya tenés el repo)
- [ ] API Key Gemini → [aistudio.google.com/apikey](https://aistudio.google.com/apikey)
- [ ] Cuentas gratis en [Neon](https://neon.tech), [Render](https://render.com), [Vercel](https://vercel.com)

Generar **JWT_SECRET** (PowerShell en Windows):

```powershell
[Convert]::ToBase64String((1..32 | ForEach-Object { Get-Random -Maximum 256 }))
```

Copiá el resultado; lo usarás en Render.

---

## PASO 1 — Base de datos en Neon (10 min)

1. Entrá a [console.neon.tech](https://console.neon.tech) → **Sign up** (con GitHub).
2. **New Project** → nombre: `smartcv` → región la más cercana → **Create**.
3. En el dashboard, buscá **Connection string** → pestaña **JDBC** o copiá la URL PostgreSQL.

Neon te da algo como:
```
postgresql://usuario:contraseña@ep-xxxx.sa-east-1.aws.neon.tech/neondb?sslmode=require
```

Anotá por separado:

| Variable | Ejemplo |
|----------|---------|
| `DB_USERNAME` | usuario de la URL |
| `DB_PASSWORD` | contraseña de la URL |
| `DATABASE_URL` | `jdbc:postgresql://ep-xxxx.sa-east-1.aws.neon.tech/neondb?sslmode=require` |

> **Importante:** `DATABASE_URL` debe empezar con `jdbc:` y **no** incluir usuario/contraseña en la URL (van en variables aparte).

---

## PASO 2 — Backend en Render (20 min)

1. [dashboard.render.com](https://dashboard.render.com) → **Sign up** con GitHub.
2. **New +** → **Web Service**.
3. Conectá el repo **LucasLedesma27/smartcv-ia**.
4. Configuración:

| Campo | Valor |
|-------|-------|
| Name | `smartcv-backend` |
| Region | la más cercana |
| Branch | `main` |
| Root Directory | `backend` |
| Runtime | **Docker** (o Java si preferís Maven) |

**Si usás Java (sin Docker):**

| Campo | Valor |
|-------|-------|
| Build Command | `mvn clean package -DskipTests` |
| Start Command | `java -jar target/smartcv-backend-1.0.0.jar` |

5. Plan: **Free**.

6. En **Environment Variables**, agregá:

| Key | Value |
|-----|-------|
| `SPRING_PROFILES_ACTIVE` | `prod` |
| `DATABASE_URL` | `jdbc:postgresql://ep-xxx.../neondb?sslmode=require` |
| `DB_USERNAME` | tu usuario Neon |
| `DB_PASSWORD` | tu contraseña Neon |
| `JWT_SECRET` | el Base64 que generaste |
| `JWT_EXPIRATION` | `86400000` |
| `GEMINI_API_KEY` | tu API key |
| `GEMINI_MODEL` | `gemini-2.5-flash` |
| `SEED_DATA` | `true` |
| `CORS_ALLOWED_ORIGINS` | `https://PLACEHOLDER.vercel.app` *(actualizar después)* |

7. **Create Web Service** y esperá el deploy (5–10 min).

8. Cuando termine, copiá la URL del servicio, ej:  
   `https://smartcv-backend.onrender.com`

9. Probá en el navegador:  
   `https://smartcv-backend.onrender.com/actuator/health`  
   Debe decir `{"status":"UP"}`.

> **Plan free:** el servicio "duerme" tras inactividad. La primera carga puede tardar **~50 segundos**.

---

## PASO 3 — Frontend en Vercel (15 min)

1. [vercel.com](https://vercel.com) → **Sign up** con GitHub.
2. **Add New → Project** → importá **smartcv-ia**.
3. Configuración:

| Campo | Valor |
|-------|-------|
| Framework Preset | Vite |
| Root Directory | `frontend` |
| Build Command | `npm run build` |
| Output Directory | `dist` |

4. **Environment Variables:**

| Key | Value |
|-----|-------|
| `VITE_API_BASE_URL` | `https://smartcv-backend.onrender.com/api` |

*(Usá tu URL real de Render + `/api` al final)*

5. **Deploy**.

6. Copiá la URL de Vercel, ej: `https://smartcv-ia.vercel.app`

---

## PASO 4 — Conectar CORS (5 min)

1. Volvé a **Render** → tu servicio backend → **Environment**.
2. Editá `CORS_ALLOWED_ORIGINS` con la URL exacta de Vercel:
   ```
   https://smartcv-ia.vercel.app
   ```
   Sin barra al final. Sin espacios.
3. Guardá → Render redeployea solo.

---

## PASO 5 — Probar como el profesor (5 min)

1. Abrí la URL de **Vercel** (no localhost).
2. Esperá si Render estaba dormido (~1 min la primera vez).
3. Login:
   - Email: `demo@smartcv.com`
   - Contraseña: `demo123`
4. Dashboard → debe mostrar datos (2 exp, 2 estudios, 5 habilidades).
5. **Analizar CV** → debe funcionar con Gemini.
6. **Carta de presentación** → probar.

Si `demo@smartcv.com` no existe, registrá un usuario nuevo y probá igual.

---

## PASO 6 — Actualizar README y subir a GitHub

En tu PC:

```powershell
cd C:\Users\lucas\OneDrive\Desktop\smartcv
# Editar README.md con las URLs reales de Vercel y Render
git add .
git commit -m "Configurar deploy producción y guía de despliegue"
git push origin main
```

---

## PASO 7 — Entregar al docente

Copiá y completá:

```
Asignatura: [nombre]
Integrantes: [nombres]

Repositorio: https://github.com/LucasLedesma27/smartcv-ia
Demo online: https://TU-APP.vercel.app

Credenciales de prueba:
  Email: demo@smartcv.com
  Contraseña: demo123

Informe técnico: docs/INFORME-TECNICO.md (en el repositorio)

Stack: React + Spring Boot + PostgreSQL + Google Gemini API
Herramientas IA en desarrollo: Cursor + Gemini
```

---

## Problemas frecuentes

| Problema | Solución |
|----------|----------|
| CORS error en Vercel | `CORS_ALLOWED_ORIGINS` = URL exacta de Vercel en Render |
| 502 / timeout al login | Esperar ~60s (Render free despertando) |
| IA no funciona | Verificar `GEMINI_API_KEY` y `GEMINI_MODEL=gemini-2.5-flash` en Render |
| Health check falla | Revisar `DATABASE_URL`, usuario y password de Neon |
| `demo@smartcv.com` no existe | `SEED_DATA=true` en Render y redeploy |
| JWT inválido tras redeploy | No cambiar `JWT_SECRET` después del primer deploy |

---

## Orden correcto

```
Neon → Render (backend) → Vercel (frontend) → CORS → Probar → README → Entregar
```

No saltes CORS; sin eso el frontend en Vercel no puede llamar al backend.
