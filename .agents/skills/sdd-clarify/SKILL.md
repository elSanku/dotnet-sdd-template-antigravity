---
name: sdd-clarify
description: Audita y revisa una spec como QA para detectar ambigüedades, huecos de máquina de estados y casos límite antes del plan técnico.
---

# Skill: SDD-Clarify

Revisa la especificación funcional (`specs/<NNN-nombre>/spec.md`) contra `docs/constitution.md` y `docs/especificacion_tecnica_ia.md` antes de autorizar el plan técnico.

## Puntos de Control
1. **Trazabilidad e Independencia de Fase**:
   - Cobertura explícita de los IDs `RN-xx`, `TR-xx` y `CA-xx` correspondientes a la fase (`SPEC-001`…`SPEC-NNN`), verificando que no dependa de entidades o flujos de una fase posterior.
2. **Invariantes de Dominio y Máquina de Estados**:
   - Fechas/horas en `UTC` (`TimeProvider`) vs `DateOnly` y reglas DST (`IsInvalidTime` / `IsAmbiguousTime`).
   - Transiciones `From → To` sin estados huérfanos ni callejones sin salida; concurrencia y orden fijo de locks.
   - Impacto exacto en cálculos/redondeos, contadores, auditoría inmutable y `EmailOutbox`.
3. **Casos Límite de UI/UX, Seguridad y Privacidad**:
   - Estados vacíos, carga y errores en Razor Pages + HTMX (con token antiforgery).
   - Confirmación explícita para acciones sensibles y validación Anti-IDOR por rol.
   - Cifrado en reposo de campos sensibles y redacción de PII en logs.
   - Textos 100% en español, contraste WCAG AA y navegación por teclado en modales y formularios.
