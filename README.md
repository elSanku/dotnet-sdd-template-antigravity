# Plantilla .NET 10 + SDD para Antigravity (`dotnet-sdd-template-antigravity`)

> **Esqueleto base para proyectos .NET 10 LTS en Google Antigravity**  
> *Plantilla lista para clonar y arrancar aplicaciones monolíticas modulares (`.NET 10` / `EF Core` / `SQL Server` / `xUnit`) bajo metodología **Spec-Driven Development (SDD)**, disciplina **Ponytail** y diseño **Anti-UI Slop**.*

---

## 🌟 Acerca de esta Plantilla

Este repositorio es el punto de partida estándar para nuevos proyectos en **.NET 10 LTS** asistidos por agentes autónomos en **Google Antigravity**, evitando copiar manualmente reglas, skills y documentación en cada inicio:

- **SDD (Spec-Driven Development):** Flujo estructurado `Constitución → Spec → Clarificación → Plan → Tareas → Implementación → Validación`, con trazabilidad `RN-xx`, `TR-xx`, `CA-xx` y marcas `//INI SPEC-XXX` / `//FIN SPEC-XXX`.
- **Ponytail:** Mínima complejidad necesaria, cero sobreingeniería y código conciso.
- **Anti-UI Slop:** Interfaces sobrias, accesibles (WCAG AA), 100% en español y sin clichés visuales genéricos.

---

## 🚀 Stack Base Incluido

### Backend, Web y Datos
- **Runtime:** [.NET 10 LTS](https://dotnet.microsoft.com/) (`net10.0`)
- **Web SSR:** ASP.NET Core **Razor Pages + HTMX** (con antiforgery global y parciales `_Partial.cshtml`)
- **Estilos:** [Tailwind CSS](https://tailwindcss.com/)
- **Datos:** Entity Framework Core (Code-First) sobre **SQL Server** (con `TimeProvider`, `HasConversion<string>()`, `CreateExecutionStrategy()`, `UPDLOCK, ROWLOCK` y cifrado Data Protection)
- **Pruebas:** [xUnit](https://xunit.net/) (`FakeTimeProvider` en unitarias + **SQL Server real** en integración)

### Seguridad y Cumplimiento (Ley 21.719 — Chile)
- **Cifrado en reposo:** `ValueConverter` con ASP.NET Core Data Protection (`DataProtectionKeys` en BD) para datos sensibles y payloads de correo.
- **Privacidad desde el diseño:** Consentimiento versionado de Términos y Privacidad, redacción obligatoria de PII en logs (`****1234`) y flujo base de baja/anonimización de cuenta.
- **Marco legal de referencia:** Si el proyecto trata datos sensibles o personales en Chile, documentar los lineamientos específicos en `docs/legal/` para que los agentes los validen antes de implementar.

---

## 🤖 Agentes SDD y Modelos Gemini 4

| Agente | Archivo | Modelo Recomendado | Responsabilidad principal |
| :--- | :--- | :--- | :--- |
| **Coordinator** | `.agents/agents/coordinator.md` | **Gemini 4 Hydrogen** (`flash`) | Orquesta fases SDD, transmite contexto y actualiza `MEMORY.md`. |
| **Planner** | `.agents/agents/planner.md` | **Gemini 4 Carbon** (`pro`) | Redacta `spec.md`, `plan.md` y `tasks.md` verificando invariantes y dependencias. |
| **Implementer** | `.agents/agents/implementer.md` | **Gemini 4 Hydrogen** (`flash`) | Ejecuta **una tarea atómica** a la vez y verifica `dotnet build` / `dotnet test`. |
| **Reviewer** | `.agents/agents/reviewer.md` | **Gemini 4 Carbon** (`pro`) | Audita specs (`sdd-clarify`) y valida implementación/seguridad/QA (`sdd-validate`). |

> **Tip:** Reserva **Gemini 4 Neon** (`flash_lite`) para subagentes auxiliares de solo lectura o diagnóstico rápido (ej. `/sdd-status` o búsqueda de referencias).

---

## 📂 Estructura del Repositorio

```text
dotnet-sdd-template-antigravity/
├── .agents/
│   ├── agents/                   # Roles SDD (coordinator, planner, implementer, reviewer)
│   └── skills/                   # 30 skills curadas (.NET 10, EF Core, xUnit, SDD, Ponytail, Anti-UI Slop)
├── docs/
│   ├── constitution.md           # Principios técnicos innegociables del proyecto
│   └── especificacion_tecnica_ia.md  # Plantilla SSOT (RN-xx, TR-xx, CA-xx, entidades, rutas, workers)
├── specs/
│   └── 001-ejemplo-base/         # Ejemplo completo de spec.md, plan.md y tasks.md
├── AGENTS.md                     # Manual operativo para los agentes en Antigravity
├── MEMORY.md                     # Memoria corta persistente entre sesiones
├── .gitignore                    # Ignorados estándar .NET y secretos locales
└── README.md
```

---

## 🛠️ Cómo Iniciar un Proyecto Nuevo (5 minutos)

1. **Crear repo desde esta plantilla** (botón **Use this template** en GitHub o `git clone`):
   ```powershell
   git clone https://github.com/elSanku/dotnet-sdd-template-antigravity.git mi-nuevo-proyecto
   cd mi-nuevo-proyecto
   ```
2. **Personalizar placeholders:** Busca `[PERSONALIZAR]` en `AGENTS.md`, `MEMORY.md`, `docs/constitution.md` y `docs/especificacion_tecnica_ia.md`, y reemplaza `<NombreProyecto>`, `<NamespaceBase>` y tu dominio.
3. **Definir el SSOT:** Completa `docs/especificacion_tecnica_ia.md` con tus reglas (`RN-xx`), transiciones (`TR-xx`), entidades EF Core y fases (`SPEC-001`…).
4. **Limpiar el ejemplo y arrancar SDD:**
   * Elimina o archiva `specs/001-ejemplo-base/` cuando inicies tu proyecto real.
   * Ejecuta `/sdd-spec 001-fundacion-bd-identidad` para redactar tu primera especificación.

---

## 👤 Autor

**Felipe Bustamante Alcavil**  
- **LinkedIn:** [felipe-bustamante-alcavil](https://linkedin.com/in/felipe-bustamante-alcavil)  
- **GitHub:** [@elSanku](https://github.com/elSanku)  
- **Email:** [felipebustamantealcavil@gmail.com](mailto:felipebustamantealcavil@gmail.com)  
