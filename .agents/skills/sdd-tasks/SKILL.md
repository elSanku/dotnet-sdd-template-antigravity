---
name: sdd-tasks
description: Desglosa el plan técnico en una lista de tareas verificables y ordenadas en specs/NNN-nombre/tasks.md.
---

# Skill: SDD-Tasks

Genera el checklist de tareas atómicas de una especificación en `specs/<NNN-nombre>/tasks.md` siguiendo `plan.md`:

## Reglas y Formato Estándar
- Máximo 20–30 minutos de trabajo por tarea, ordenadas estrictamente por dependencia (Dominio/BD → Aplicación/Infraestructura → Web/UI → Pruebas).
- Si resultan más de 10 tareas, propón dividir la spec antes de continuar.
- Cada tarea debe referenciar los requisitos funcionales (`RF-x`) y reglas/transiciones del SSOT (`RN-xx`, `TR-xx`) que cubre y tener un criterio de aceptación verificable (`dotnet build`, `dotnet test` nombrando el ID o comprobación objetiva):

```markdown
- [ ] **T1. <Descripción concisa de la tarea>.** RF-1, RF-2 (`RN-01`, `RN-02`)
  - Hecho cuando: <comprobación verificable con dotnet build / dotnet test / endpoint>.
- [ ] **T2. <Descripción concisa de la tarea>.** RF-3 (`TR-01`)
  - Hecho cuando: <comprobación verificable>.
```
