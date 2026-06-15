# SmartCV IA — Datos para evaluación

**Grupo:** No Signal

## Repositorio
https://github.com/LucasLedesma27/smartcv-ia

## Demo online
**URL:** https://smartcv-ia.vercel.app

**Backend (API):** https://smartcv-ia.onrender.com

## Credenciales de prueba
| Campo | Valor |
|-------|-------|
| Email | `demo@smartcv.com` |
| Contraseña | `demo123` |

El usuario demo incluye perfil profesional, experiencias, educación y habilidades precargadas.

## Funcionalidades a probar
1. Login con las credenciales anteriores
2. Dashboard (estadísticas del CV)
3. Perfil profesional, experiencias, educación, habilidades (CRUD)
4. **Analizar CV con IA** (menú lateral)
5. **Generar carta de presentación** (menú lateral)

## Informe técnico
`docs/INFORME-TECNICO.md` en el repositorio.

## Stack tecnológico
- Frontend: React, Vite, Material UI
- Backend: Spring Boot 3, JWT, Spring Security
- Base de datos: PostgreSQL (Neon)
- IA: Google Gemini API
- DevOps: Docker, GitHub Actions

## Integrantes

| # | Nombre |
|---|--------|
| 1 | Lucas Ledesma |
| 2 | Juan Pablo Gómez |
| 3 | Leandro Paredes |
| 4 | Francisco Mestre |

## Nota sobre el plan free de Render
La primera carga del backend puede tardar ~50 segundos si el servicio estuvo inactivo. Antes de probar la app, se puede abrir https://smartcv-ia.onrender.com/actuator/health y esperar a que responda `UP`.

## Nota sobre Google Gemini (plan gratuito)
El análisis de CV y la carta de presentación dependen de la API de Gemini. En horarios de mucha demanda puede aparecer un error temporal (503). En ese caso, esperar 30 segundos y volver a intentar.
