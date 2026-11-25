---
name: sdd-plan
description: Diseña y genera el plan técnico (.NET 10, arquitectura por capas, EF Core, Razor Pages + HTMX y pruebas xUnit) en specs/NNN-nombre/plan.md.
---

# Skill: SDD-Plan

Define el diseño técnico detallado de una especificación respetando `docs/constitution.md`, `AGENTS.md` y `docs/especificacion_tecnica_ia.md`:

## Estructura del Plan Técnico
1. **Arquitectura por Capas (.NET 10)**:
   - `Domain/`: Entidades, enums cerrados, invariantes puros, cálculos y validadores (sin EF Core ni Web).
   - `Application/`: Casos de uso con `TimeProvider` inyectado, contratos (`IEmailSender`, `ISensitiveDataProtector`, etc.) y validaciones.
   - `Infrastructure/`: `ApplicationDbContext` (EF Core / SQL Server con `.HasConversion<string>().HasMaxLength(40)`, cifrado Data Protection con claves persistidas, `CreateExecutionStrategy()`), consultas con `UPDLOCK, ROWLOCK` en orden fijo, auditoría, `EmailOutbox` y workers idempotentes.
   - `Web/`: Rutas Razor Pages y Page Handlers HTMX (`_Partial.cshtml`), antiforgery global (`htmx:configRequest`), rate limiting, filtros Anti-IDOR y Tailwind CSS.
2. **Modelo de Datos, Zona Horaria, Concurrencia y Seguridad**:
   - Tablas e índices únicos filtrados afectados; manejo determinista de DST; transiciones atómicas verificando `rowsAffected == 1`; cifrado en reposo y redacción de PII en logs.
3. **Trazabilidad de Decisiones**:
   - Decisiones justificadas con su alternativa descartada y matriz de trazabilidad `RF-x` → `RN-xx` / `TR-xx` / `CA-xx`.
4. **Estrategia de Verificación y Pruebas**:
   - `UnitTests` (xUnit + `FakeTimeProvider`) y `IntegrationTests` contra **SQL Server real** (LocalDB o Testcontainers; nunca `InMemory` ni `SQLite` para concurrencia o índices filtrados), nombrando cada test con el ID `RN-xx` / `TR-xx` / `CA-xx`.
