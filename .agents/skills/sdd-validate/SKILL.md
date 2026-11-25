---
name: sdd-validate
description: Valida una spec completa RF por RF contra el código .NET 10, compilación, pruebas xUnit y criterios de aceptación. Uso - /sdd-validate NNN-nombre.
---

# Skill: Validación de Spec SDD (/sdd-validate)

Usa este procedimiento para auditar y validar una spec completa una vez que todas sus tareas han sido implementadas, contrastando cada requisito contra el código real.

## Contexto de Entrada
- **Uso**: `/sdd-validate NNN-nombre`
- Spec a auditar: `specs/<NNN-nombre>/spec.md`
- Checklist de tareas: `specs/<NNN-nombre>/tasks.md`

---

## Procedimiento Obligatorio

### 1. Auditoría Requisito por Requisito (`RF-x`, `RN-xx`, `TR-xx`, `CA-xx`)
Recorre `specs/<NNN-nombre>/spec.md` y verifica cada requisito funcional y no funcional:

1. **Evidencia en Código y Trazabilidad**:
   - Indica qué archivos en `Domain/`, `Application/`, `Infrastructure/`, `Web/` y `Tests/` cubren el requisito, verificando marcas `//INI SPEC-XXX` y `//FIN SPEC-XXX` y tests nombrados por `RN-xx` / `TR-xx`.
2. **Verificación de Compilación y Pruebas**:
   - Ejecuta `dotnet build` para comprobar compilación limpia sin errores.
   - Ejecuta `dotnet test` (unitarias con `FakeTimeProvider` e integración contra SQL Server real) y reporta su resultado.
3. **Invariantes Técnicos y de Constitución**:
   - Cero llamadas directas a `DateTime.UtcNow` / `DateTime.Now` (uso exclusivo de `TimeProvider`).
   - Enums con `HasConversion<string>()`, `CreateExecutionStrategy()` en transacciones manuales, orden fijo de locks y `rowsAffected == 1`.
   - Antiforgery en HTMX, Anti-IDOR, sanitización XSS, cifrado en reposo de campos sensibles y redacción de PII en logs.
   - Textos 100% en español, accesibilidad WCAG AA y diseño responsive con Tailwind CSS.

---

## Regla de Oro
> **SI ALGÚN RF / RN / TR NO ESTÁ CUBIERTO O PRESENTA FALLOS, REVÉLALO CLARAMENTE**.
> **NO MODIFIQUES NI ARREGLES CÓDIGO** durante este comando: tu rol es exclusivamente auditar y reportar con honestidad técnica.

---

### 2. Evaluación de Criterios de Finalización y Veredicto
Emite un veredicto estructurado:

| Requisito / Regla | Estado | Evidencia / Test | Observación |
| :--- | :---: | :--- | :--- |
| **RF-1 (`RN-01`)** | ✅ Cumplido | `Domain/Entities/...` | Regla verificada con test xUnit |
| **RF-2 (`TR-01`)** | ⚠️ Parcial | `Infrastructure/...` | Falta test de caso límite DST |

**Veredicto Final**:
- ¿La spec cumple su Definition of Done para pasar a `Estado: implementada` y generar el informe técnico en `docs/` (`.md` y `.html`)?
- Si faltan puntos, entrega la lista exacta de pendientes antes del cierre.
