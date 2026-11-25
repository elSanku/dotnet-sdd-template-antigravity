---
name: sdd-spec
description: Entrevista al usuario y genera la spec inicial para una funcionalidad en specs/NNN-nombre/spec.md. Usa la metodología SDD.
---

# Skill: Especificación SDD (/sdd-spec)

Usa este procedimiento para entrevistar al usuario y generar la especificación inicial de un requerimiento dentro de `specs/NNN-nombre/spec.md`.

## Contexto de Entrada
- **Uso**: `/sdd-spec NNN-nombre idea inicial`
- Carpeta destino: `specs/<NNN-nombre>/`

---

## Procedimiento Obligatorio

### 1. Preparación y Regla de No-Código
- **PROHIBIDO escribir o modificar código** durante este paso.
- Lee [`docs/constitution.md`](docs/constitution.md), [`docs/especificacion_tecnica_ia.md`](docs/especificacion_tecnica_ia.md), [`MEMORY.md`](MEMORY.md) y [`.agents/skills/sdd/SKILL.md`](.agents/skills/sdd/SKILL.md).

### 2. Entrevista de Clarificación (Preguntas de UNA en UNA)
1. Analiza la idea inicial e identifica ambigüedades, puntos ciegos, casos de error y aspectos fuera de alcance.
2. Haz preguntas al usuario **de UNA en UNA** (máximo 5 preguntas en total).
3. Espera la respuesta del usuario antes de formular la siguiente. Si el usuario pide generar la spec directamente, procede al paso 3.

### 3. Generación del Archivo `specs/<NNN-nombre>/spec.md`
Genera `spec.md` con:
- `Estado: borrador`
- `Reglas SSOT cubiertas: RN-xx, TR-xx, CA-xx`
- **Contexto y objetivo**, **Usuarios** e **Historias de usuario** (`Como <rol>, quiero <acción> para <beneficio>`).
- **Requisitos funcionales en formato EARS (en español)** vinculados a `RN-xx` / `TR-xx`.
- **Requisitos no funcionales, casos límite (`CA-xx` Given/When/Then) y fuera de alcance**.
- **Criterios de finalización (DoD) y dudas abiertas**.

### 4. Regla de Pureza de la Spec
> La spec describe exclusivamente el **QUÉ** y el **POR QUÉ**.
> **Prohibido incluir detalles de implementación**: nada de clases, tablas SQL ni archivos de código (eso va en `plan.md`).

### 5. Cierre del Paso
Actualiza brevemente [`MEMORY.md`](MEMORY.md) indicando la nueva spec en estado borrador y presenta un resumen al usuario para su aprobación.
