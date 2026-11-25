---
name: sdd-change
description: Gestiona cambios o nuevos requisitos en una spec activa aplicando la disciplina SDD.
---

# Skill: SDD-Change

Permite introducir modificaciones a una especificación activa sin alterar código a ciegas, manteniendo coherencia con `docs/especificacion_tecnica_ia.md`, `docs/constitution.md` y `AGENTS.md`.

## Proceso
1. **Alineación con SSOT y Spec (`spec.md`)**:
   - Si el cambio altera una regla global del negocio, actualizar primero `docs/especificacion_tecnica_ia.md` (y `docs/constitution.md` / `AGENTS.md` si afecta invariantes).
   - Ajustar los requisitos funcionales (formato EARS) y criterios de aceptación en `specs/<NNN-nombre>/spec.md`.
2. **Impacto en el Plan Técnico (`plan.md`)**:
   - Analizar entidades/migraciones EF Core (`Domain/`, `Infrastructure/`), servicios (`Application/`), workers o Razor Pages / parciales HTMX y estilos Tailwind (`Web/`) afectados.
3. **Ajuste de Tareas (`tasks.md`)**:
   - Agregar o modificar las tareas atómicas conservando el orden de dependencia, la trazabilidad por `RF-x` (`RN-xx`/`TR-xx`) y la verificación con `dotnet build` y `dotnet test`.
