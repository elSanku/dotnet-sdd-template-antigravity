# Plantilla .NET 10 + SDD (`dotnet-sdd-template`)

Repositorio esqueleto para iniciar proyectos en **.NET 10 LTS** bajo metodología **Spec-Driven Development (SDD)**, disciplina **Ponytail**, diseño **Anti-UI Slop** y flujo multi-agente con modelos diferenciados.

## Qué incluye esta plantilla

* **`AGENTS.md`**: Reglas operativas del repositorio (stack, convenciones, concurrencia, marcas `//INI SPEC-XXX`, comandos y verificación).
* **`MEMORY.md`**: Memoria corta de sesión para conservar estado entre conversaciones.
* **`docs/constitution.md`**: Principios innegociables del proyecto.
* **`docs/especificacion_tecnica_ia.md`**: Plantilla de Fuente Única de Verdad (SSOT) con IDs `RN-xx`, `TR-xx`, `CA-xx`, matriz de estados, modelo de datos, rutas, workers y fases `SPEC-001`…`SPEC-NNN`.
* **`specs/001-ejemplo-base/`**: Ejemplo completo de `spec.md`, `plan.md` y `tasks.md` listo para usar como referencia.
* **`.agents/agents/`**: 4 agentes SDD preconfigurados con su modelo óptimo:
  * `coordinator.md` → **Gemini 4 Hydrogen** (`flash`)
  * `planner.md` → **Gemini 4 Carbon** (`pro`)
  * `implementer.md` → **Gemini 4 Hydrogen** (`flash`)
  * `reviewer.md` → **Gemini 4 Carbon** (`pro`)
  * *(Nota: **Gemini 4 Neon** / `flash_lite` queda recomendado para subagentes auxiliares de solo lectura, búsqueda o consulta de estado `/sdd-status`).*
* **`.agents/skills/`**: 30 skills curadas para `.NET 10`, `Razor Pages + HTMX`, `EF Core`, `xUnit`, `Ponytail`, `Anti-UI Slop` y flujo `SDD`.

## Cómo iniciar un nuevo proyecto (Checklist de 5 minutos)

1. **Copia esta carpeta** con el nombre de tu nuevo proyecto (ej. `mi-nuevo-proyecto`) e inicializa Git (`git init`).
2. **Busca `[PERSONALIZAR]`** en todos los `.md` y reemplaza los placeholders (`<NOMBRE_PROYECTO>`, `<NamespaceBase>`, `<ZonaHoraria>`, etc.).
3. **Define tu dominio en `docs/especificacion_tecnica_ia.md`**:
   * Completa el Glosario, tus reglas `RN-xx`, transiciones `TR-xx`, entidades EF Core, rutas Razor Pages / APIs y el Plan por Fases (`SPEC-001`…).
4. **Sincroniza `docs/constitution.md`, `AGENTS.md` y `MEMORY.md`** con las reglas fijas de tu nuevo proyecto.
5. **Limpia el ejemplo y comienza SDD**:
   * Elimina o archiva `specs/001-ejemplo-base/` cuando empieces tu primera spec real.
   * Ejecuta `/sdd-spec 001-fundacion-bd-identidad` para redactar tu primera especificación.
