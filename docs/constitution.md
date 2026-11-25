# Constitución del Proyecto — [PERSONALIZAR: <Nombre del Proyecto>]

Este documento establece los **principios innegociables** que rigen toda especificación (`specs/`), plan técnico, tarea e implementación. La Fuente Única de Verdad (SSOT) detallada es [`docs/especificacion_tecnica_ia.md`](especificacion_tecnica_ia.md).

## 1. Disciplina Spec-Driven Development (SDD) y Trazabilidad
* **Spec-First:** Ninguna funcionalidad o regla se codifica sin estar aprobada primero en `specs/NNN-nombre/spec.md`, alineada con las fases `SPEC-001`…`SPEC-NNN` de `docs/especificacion_tecnica_ia.md`.
* **Trazabilidad completa (`SPEC-XXX`, `RN-xx`, `TR-xx`):**
  * `spec.md` define el **QUÉ** y el **POR QUÉ** en formato EARS referenciando los IDs `RN-xx` / `TR-xx` y criterios `CA-xx`.
  * `plan.md` define el **CÓMO** por capas (`Domain`, `Application`, `Infrastructure`, `Web`).
  * `tasks.md` desglosa entregas atómicas verificables sin dependencias hacia fases futuras.
  * El código delimita bloques con `//INI SPEC-XXX` y `//FIN SPEC-XXX` (sin sufijo de tarea) y las pruebas xUnit nombran el `RN-xx` / `TR-xx` verificado.

## 2. Idioma 100% Español
* Toda interfaz de usuario, etiquetas, validaciones, correos transaccionales, especificaciones, planes y mensajes de error para el usuario deben estar íntegramente en **español**.

## 3. Stack Tecnológico y Reglas Técnicas (.NET 10 Puro)
* **Backend y Web:** C# / ASP.NET Core (.NET 10 LTS) usando exclusivamente **Razor Pages + HTMX** (Page Handlers + vistas parciales `_Partial.cshtml`, con token antiforgery global en cada mutación HTMX). Prohibido introducir MVC Controllers de vistas, Blazor o SPAs externas (React/Angular/Vue).
* **Datos y Concurrencia:** Entity Framework Core sobre **SQL Server** con enums persistidos como string (`HasConversion<string>()`), `CreateExecutionStrategy()` en transacciones manuales, orden fijo de locks `UPDLOCK, ROWLOCK` y actualizaciones condicionales atómicas verificando `rowsAffected == 1`.
* **Reloj y Pruebas Reales:** Uso obligatorio de `TimeProvider` (prohibido `DateTime.UtcNow` directo). Pruebas unitarias en **xUnit** (`FakeTimeProvider`) y pruebas de integración/concurrencia contra **SQL Server real** (LocalDB o Testcontainers; prohibido `InMemory` o `SQLite` para locks e índices filtrados).
* **Estilos:** **Tailwind CSS** respetando principios Anti-UI Slop y accesibilidad WCAG AA.
* **Workers:** Consolidados en `BackgroundService` idempotentes e inmunes a DST (evaluación por fecha local o estado en BD, nunca por hora exacta susceptible a saltos de reloj).

## 4. Invariantes Innegociables del Dominio
* **Zona horaria y DST:** Persistencia en `UTC` (`DateTime`) y fechas calendario en `DateOnly` (`TodayLocal` vía `ConvertTimeFromUtc`); manejo explícito de `IsInvalidTime` e `IsAmbiguousTime` al convertir desde `[PERSONALIZAR: America/Santiago]`.
* **[PERSONALIZAR: Invariantes de negocio]:**
  * Ej. Roles exclusivos en ASP.NET Core Identity (`User`, `Admin`).
  * Ej. Regla de redondeo monetario (`MidpointRounding.AwayFromZero`).
  * Ej. Outbox transaccional (`EmailOutbox`) y auditoría inmutable para acciones críticas.

## 5. Seguridad, Privacidad y Almacenamiento
* **Cifrado en reposo y cero PII en logs:** Datos sensibles cifrados en aplicación (ASP.NET Core Data Protection con claves persistidas en BD); redacción obligatoria de identificadores y secretos en logs.
* **Protección Web y Medios:** Antiforgery global en HTMX, validación Anti-IDOR en cada endpoint/handler, sanitización estricta contra XSS y política explícita de almacenamiento de archivos (URL HTTPS externa o validación estricta de MIME/tamaño).
