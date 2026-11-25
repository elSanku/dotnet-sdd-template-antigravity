# Memoria del Proyecto (SDD) — [PERSONALIZAR: <Nombre del Proyecto>]

## Estado General
* **SSOT:** `docs/especificacion_tecnica_ia.md` (v1 — borrador inicial / listo para SDD).
* **Constitución:** `docs/constitution.md` (sincronizada con SSOT).
* **Fase actual:** Preparación / Listo para iniciar `SPEC-001` (`specs/001-.../spec.md`).

## Decisiones Clave Consolidadas
* [PERSONALIZAR: Ej. Stack cerrado en .NET 10 LTS + Razor Pages + HTMX + Tailwind CSS + EF Core / SQL Server + xUnit.]
* [PERSONALIZAR: Ej. Reglas críticas del dominio resumidas en 3–5 viñetas.]

## Errores a Evitar
* No usar `DateTime.UtcNow` directo (usar `TimeProvider`).
* No probar locks `UPDLOCK, ROWLOCK` ni índices filtrados en `InMemory`/`SQLite` (usar SQL Server real).
* No adelantar entidades ni flujos de fases futuras fuera de la spec activa.

## Specs Activas
* *(Ninguna spec iniciada aún — ejemplo de referencia en `specs/001-ejemplo-base/`).*
