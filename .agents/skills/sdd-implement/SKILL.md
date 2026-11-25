---
name: sdd-implement
description: Implementa UNA sola tarea de una spec respetando el plan técnico y la constitución (.NET 10).
---

# Skill: SDD-Implement

Guía al agente implementador en la ejecución atómica de una tarea del proyecto (.NET 10 + SDD).

## Reglas
1. Ejecutar **solo** la tarea asignada en `specs/NNN-nombre/tasks.md` respetando `docs/constitution.md`, `AGENTS.md` y `docs/especificacion_tecnica_ia.md`.
2. Seguir las directrices de Ponytail (código C# / Razor Pages + HTMX limpio, conciso y sin sobre-ingeniería) cumpliendo las reglas técnicas obligatorias:
   - Usar siempre `TimeProvider` (prohibido `DateTime.UtcNow` / `DateTime.Now`).
   - Envolver transacciones explícitas en `CreateExecutionStrategy()`, adquirir locks `UPDLOCK, ROWLOCK` en el orden fijo del proyecto, y verificar `rowsAffected == 1` en transiciones de estado.
   - Mantener antiforgery en HTMX, validación Anti-IDOR, cifrado Data Protection en datos sensibles y cero PII sin enmascarar en logs.
3. Estilos con **Tailwind CSS** cuidando contraste, accesibilidad (WCAG AA) y principios Anti-UI Slop; textos visibles 100% en español.
4. Respetar las marcas de auditoría unificadas en el código (sin sufijo de tarea), preservando intactas las marcas históricas de specs previas:
   `//INI SPEC-XXX`
   `//FIN SPEC-XXX`
5. Verificar que la solución compila limpiamente (`dotnet build`) y que todas las pruebas unitarias/integración pasan (`dotnet test`, nombrando los tests con `RN-xx` / `TR-xx`).
6. Marcar la tarea como completada en `tasks.md` (`- [x] **Tn...**`), actualizar si corresponde `MEMORY.md`, y detenerse.
