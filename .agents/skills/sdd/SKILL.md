---
name: sdd
description: Úsala siempre que trabajes con Spec-Driven Development en este proyecto (docs/constitution.md o cualquier archivo dentro de specs/) - redactar, revisar o cambiar specs, planes y tareas, o implementar y validar tareas de una spec.
---

# Spec-Driven Development (SDD) — Plantilla .NET 10

## Flujo
Constitución → Spec → Clarificación → Plan → Tareas → Implementación → Validación → Cambio.
- Nunca pases a la siguiente fase sin la aprobación explícita del usuario.
- La spec manda: se alinea con `docs/especificacion_tecnica_ia.md` referenciando sus IDs canónicos (`RN-xx`, `TR-xx`, `CA-xx`). Si falta una decisión de negocio, para y pregunta.
- Un cambio de requisitos se hace primero en el SSOT / `spec.md`, luego en `plan.md` y `tasks.md`, y por último en el código.
- Cada fase (`SPEC-001`…`SPEC-NNN`) vive en su carpeta: `specs/NNN-nombre/` con `spec.md`, `plan.md` y `tasks.md`, sin depender nunca de fases posteriores.
- Al terminar cada fase, actualiza `MEMORY.md` y genera el informe técnico `.md` y `.html` en `docs/` antes del commit final.

## Plantilla de spec (spec.md)
```markdown
# Spec NNN — <Nombre> (SPEC-NNN)

Estado: borrador | aprobada | implementada
Reglas SSOT cubiertas: RN-xx, TR-xx, CA-xx

## Contexto y objetivo
## Usuarios
## Historias de usuario
- HU-1. Como <rol>, quiero <acción> para <beneficio>.
## Definiciones (solo si hay términos adicionales al Glosario SSOT)
## Requisitos funcionales (EARS vinculados a RN-xx / TR-xx)
## Requisitos no funcionales (seguridad, privacidad, concurrencia, accesibilidad)
## Casos límite y Criterios de Aceptación (Given / When / Then)
## Fuera de alcance
## Criterios de finalización (Definition of Done)
## Dudas abiertas
- [NECESITA ACLARACIÓN] <duda>
```
La spec describe el QUÉ y el POR QUÉ en lenguaje de dominio. Los detalles de capas, clases y SQL van en `plan.md`.

## Requisitos en EARS (en español)
- RF-x (`RN-xx` / `TR-xx`): CUANDO <evento>, EL SISTEMA <respuesta>.
- RF-x (`RN-xx` / `TR-xx`): SI <condición no deseada>, ENTONCES EL SISTEMA <respuesta>.
- RF-x (`RN-xx` / `TR-xx`): MIENTRAS <estado>, EL SISTEMA <respuesta>.
- RF-x (`RN-xx` / `TR-xx`): EL SISTEMA <comportamiento permanente>.
Cada RF debe ser verificable y medible.

## Plan (plan.md)
Responsabilidades por proyecto (`Domain`, `Application`, `Infrastructure`, `Web`, `UnitTests`, `IntegrationTests`) · Modelo EF Core / SQL Server (`HasConversion<string>()`, cifrado Data Protection, `CreateExecutionStrategy()`, orden fijo de locks) · `TimeProvider` y manejo de zona horaria/DST · Rutas Razor Pages y Page Handlers HTMX con antiforgery y Anti-IDOR · Estrategia de verificación (`dotnet build` y `dotnet test` sobre `FakeTimeProvider` y SQL Server real).

## Tareas (tasks.md)
```markdown
- [ ] **Tn. <Descripción>.** RF-x (`RN-xx`, `TR-xx`)
  - Hecho cuando: <comprobación verificable + nombre de test xUnit>.
```
Máximo 20–30 min por tarea, en estricto orden de dependencia. Si salen más de 10, propón dividir la spec.

## Implementación
Una sola tarea cada vez: escribir el código delimitado con `//INI SPEC-XXX` y `//FIN SPEC-XXX` (preservando marcas previas intactas), verificar compilación limpia (`dotnet build`) y pruebas xUnit (`dotnet test`), marcar la tarea en `tasks.md` y parar.
