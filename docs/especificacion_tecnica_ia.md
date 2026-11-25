# Especificación Técnica Maestra y Prompt de Contexto para Agente de IA (SSOT) — [PERSONALIZAR: v1]
## [PERSONALIZAR: <Nombre del Proyecto> — Subtítulo del Sistema]

**Stack obligatorio:** C# / ASP.NET Core (.NET 10 LTS), Razor Pages con HTMX, Entity Framework Core, SQL Server, Tailwind CSS, xUnit.

> **Instrucción directa para el Agente de IA:** Este documento es la Fuente Única de Verdad (SSOT). No inventes reglas de negocio fuera de este documento. Implementa estrictamente las reglas identificadas (`RN-01`…`RN-xx`), la matriz de transiciones (`TR-01`…`TR-xx`), los bloqueos de concurrencia, la seguridad y los criterios de aceptación descritos aquí. Los puntos marcados como **`[PENDIENTE Pn]`** (sección 9) tienen un valor por defecto obligatorio que debes usar sin dejar `TODO` en el código. Si aparece un caso genuinamente no cubierto, detente y consulta antes de codificar.

### Convención de Trazabilidad SDD
* **Fases / Specs (`SPEC-001`…`SPEC-NNN`):** Cada fase del Plan de Construcción (sección 8) corresponde a una carpeta `specs/NNN-nombre/`. Todo bloque implementado lleva marcas `//INI SPEC-XXX` y `//FIN SPEC-XXX` (sin sufijo de tarea).
* **Reglas (`RN-xx`) y Transiciones (`TR-xx`):** Los planes (`plan.md`), tareas (`tasks.md`) y nombres de pruebas xUnit referencian los IDs cubiertos (ej. `RN01_ValidarRegla_ComportamientoEsperado`).

### Glosario Canónico [PERSONALIZAR]

| Término | Significado exacto |
| :--- | :--- |
| **[Ejemplo: Solicitud / Orden]** | Entidad principal identificada por `Id` (`Guid`) con máquina de estados atómica. |
| **[Ejemplo: Monto base]** | Cálculo base redondeado a entero (`MidpointRounding.AwayFromZero`). |

---

## 1. Arquitectura, Reglas Técnicas (.NET 10 / EF Core / SQL Server) y Seguridad

### 1.1 Estructura de la Solución (Monolito Modular por Capas)
* `src/<NamespaceBase>.Domain`: Entidades, enums, invariantes puros y validadores. Cero dependencias a EF Core o Web.
* `src/<NamespaceBase>.Application`: Casos de uso / comandos con `TimeProvider`, contratos (`IEmailSender`, `ISensitiveDataProtector`, etc.) y DTOs.
* `src/<NamespaceBase>.Infrastructure`: `ApplicationDbContext` (EF Core / SQL Server), consultas transaccionales (`UPDLOCK, ROWLOCK`), cifrado Data Protection, integraciones externas y `BackgroundService`.
* `src/<NamespaceBase>.Web`: Razor Pages, Page Handlers HTMX (`_Partial.cshtml`), autorización, antiforgery, rate limiting y Tailwind CSS. Prohibido agregar MVC Controllers de vistas, Blazor o SPAs externas.
* `tests/<NamespaceBase>.UnitTests`: Pruebas unitarias xUnit usando `FakeTimeProvider`.
* `tests/<NamespaceBase>.IntegrationTests`: Pruebas de integración y concurrencia xUnit contra **SQL Server real** (LocalDB o Testcontainers; prohibido `InMemory`/`SQLite` para locks o índices filtrados).

### 1.2 Reglas Técnicas Obligatorias en .NET 10 / EF Core / SQL Server
* **Reloj inyectado (`TimeProvider`):** Prohibido usar `DateTime.UtcNow`, `DateTime.Now` o `DateTimeOffset.UtcNow` directamente. Toda lectura proviene de `TimeProvider.GetUtcNow().UtcDateTime`.
* **Zona horaria y DST (`[PERSONALIZAR: America/Santiago]`):** Persistir instantes en `UTC` (`DateTime`) y fechas puras en `DateOnly`. Evaluar `TodayLocal` con `ConvertTimeFromUtc`; al convertir local → UTC manejar `IsInvalidTime` y `IsAmbiguousTime` (`Max()` offset).
* **Enums como string:** Configurar todo enum persistido con `.HasConversion<string>().HasMaxLength(40)`.
* **Reintentos y transacciones manuales:** Envolver `BeginTransactionAsync` en `db.Database.CreateExecutionStrategy().ExecuteAsync(...)`.
* **Orden fijo de locks (anti-deadlock):** Adquirir `WITH (UPDLOCK, ROWLOCK)` invariablemente en el orden: `[PERSONALIZAR: 1.º RecursoPadre → 2.º ApplicationUser → 3.º EntidadHija]`.
* **Transiciones atómicas:** Ejecutar `ExecuteUpdateAsync` con `WHERE Id = @id AND Status = @estadoEsperado` (y `RowVersion`), verificando `rowsAffected == 1`.

### 1.3 Seguridad, Antiforgery HTMX y Privacidad
* **Roles en Identity:** ASP.NET Core Identity (`AspNetRoles` / `AspNetUserRoles`, roles definidos: `[PERSONALIZAR: User, Admin]`, sin columna `Role` duplicada).
* **Anti-IDOR y Antiforgery HTMX:** Validar propietario/rol en cada handler (`403`/`404`) e inyectar `RequestVerificationToken` globalmente vía `htmx:configRequest`.
* **Rate Limiting y XSS:** Limitar endpoints sensibles (`/cuenta/login`, creación de recursos) y sanitizar texto enriquecido antes de renderizar.
* **Cifrado en reposo y Redacción PII:** Cifrar campos sensibles (`ValueConverter` con ASP.NET Core Data Protection + `DataProtectionKeys` en BD) y enmascarar PII en `ILogger` / `LastError`.

---

## 2. Reglas de Negocio Numeradas (`RN-01` a `RN-xx`) [PERSONALIZAR]

> Reemplaza los ejemplos de abajo por las reglas reales de tu proyecto manteniendo el formato `RN-xx`.

* **`RN-01` (Identidad y roles):** [Ejemplo: Todo usuario se registra con correo verificado, rol único en Identity y aceptación versionada de Términos y Privacidad.]
* **`RN-02` (Regla de negocio principal):** [Ejemplo: La creación de un registro requiere validación transaccional sin solapamientos bajo `UPDLOCK, ROWLOCK`.]
* **`RN-03` (Cálculo / Tarifa / Plazo):** [Ejemplo: Todo monto se calcula y redondea con `MidpointRounding.AwayFromZero` quedando inmutable tras confirmar.]

---

## 3. Matriz Completa de Transiciones (`TR-01` a `TR-xx`) [PERSONALIZAR]

| ID | Estado Origen (`From`) | Estado Destino (`To`) | Actor | Guard / Condición obligatoria | Efectos atómicos en la misma transacción |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `TR-01` | *(Nuevo)* | `Active` | `User` | Validaciones de entrada y disponibilidad sin conflicto. | Crea entidad, registra `AuditLog` y encola notificación en `EmailOutbox`. |
| `TR-02` | `Active` | `Cancelled` | `User` / `Admin` | `UtcNow <= CancelDeadlineUtc`, motivo obligatorio. | Libera recurso, registra `AuditLog` y encola aviso en `EmailOutbox`. |

---

## 4. Modelo de Datos Completo (EF Core y Catálogos) [PERSONALIZAR]

### 4.1 Catálogos Cerrados (`.HasConversion<string>().HasMaxLength(40)`)
* **`[EjemploStatus]`:** `Active`, `Cancelled`, `Completed`.
* **`EmailOutboxStatus`:** `Pending`, `Sent`, `FailedPermanent`.

### 4.2 Entidades

| # | Entidad | Propiedades clave | Índices, cifrado e integridad |
| :-- | :--- | :--- | :--- |
| 1 | `ApplicationUser` | `Id`, `Email`, `FullName`, `AcceptedTermsVersion`, `AcceptedTermsAtUtc` | Hereda de `IdentityUser`. Único en `Email`. |
| 2 | `GlobalParameter` | `Key` (PK), `Value`, `UpdatedAtUtc` | Parámetros operativos y fecha `LastDailyOpsRunDate`. |
| 3 | `[EjemploEntity]` | `Id` (`Guid`), `OwnerId`, `StartDateTimeUtc`, `EndDateTimeUtc`, `Status`, `RowVersion` | Índice único filtrado según reglas de negocio. |
| 4 | `EmailOutbox` | `Id` (`Guid`), `TemplateKey`, `ToEmail`, `Subject`, `PayloadJson` (**cifrado**), `Status`, `AttemptCount`, `NextAttemptAtUtc`, `LastError`, `CreatedAtUtc`, `SentAtUtc` | Índice filtrado `(NextAttemptAtUtc) WHERE Status = 'Pending'`. |

---

## 5. Mapa de Rutas Razor Pages y Handlers HTMX [PERSONALIZAR]

| Ruta Razor Page | Rol Autorizado | Propósito y Page Handlers HTMX principales |
| :--- | :--- | :--- |
| `/` (`Index`) | Público | Portada / listado principal con filtro HTMX (`OnGetList`). |
| `/cuenta/login`, `/cuenta/registro` | Público | Autenticación y registro con validación y rate limiting. |
| `/panel` | `User` | Gestión de registros del usuario (`OnPostCreate`, `OnPostCancel`). |
| `/admin` | `Admin` | Administración, parámetros globales y auditoría. |

---

## 6. Workers Consolidados (`BackgroundService` Idempotentes) [PERSONALIZAR]

| # | Worker | Cadencia | Sub-tareas idempotentes |
| :-- | :--- | :--- | :--- |
| 1 | `[OutboxAndLifecycleWorker]` | Cada **5 min** *(+ canal post-commit)* | Despacho `EmailOutbox` (máx. 5 intentos con backoff) + expiraciones de estado atómicas. |
| 2 | `[DailyOperationsWorker]` | Tick cada **15 min** + al arrancar *(si `LastDailyOpsRunDate < TodayLocal`)* | Tareas diarias inmunes a DST + actualización de `LastDailyOpsRunDate`. |

---

## 7. Criterios de Aceptación Base (Given / When / Then para Suite xUnit) [PERSONALIZAR]

* **`CA-01` (`RN-02`, Concurrencia):** *Dada* una reserva o recurso ocupado, *cuando* dos peticiones concurrentes intentan tomar el mismo recurso, *entonces* SQL Server serializa con `UPDLOCK, ROWLOCK` y permite solo una operación devolviendo conflicto en la segunda.

---

## 8. Plan de Construcción por Fases (`SPEC-001`…`SPEC-NNN`, sin dependencias rotas) [PERSONALIZAR]

1. **Fase 1 (`SPEC-001`) — Fundación, BD, Identidad, Seguridad y Outbox:**
   * Alcance: Solución de 4 capas + 2 proyectos de test, `ApplicationDbContext`, Identity, `TimeProvider`, antiforgery HTMX, cifrado Data Protection y `EmailOutbox` + worker base.
   * **DoD:** Migración aplica limpia en SQL Server real; tests de seguridad y outbox en verde.
2. **Fase 2 (`SPEC-002`) — [PERSONALIZAR: Módulo Principal de Negocio]:**
   * Alcance y DoD sin depender de fases posteriores.

---

## 9. Decisiones con Valor por Defecto Provisional (`[PENDIENTE Pn]`) [PERSONALIZAR]

| ID | Tema | Valor por defecto obligatorio en el MVP |
| :--- | :--- | :--- |
| `P1` | [Ejemplo: Hosting de workers] | Hosting *always-on* + recuperación al arranque (`LastDailyOpsRunDate < TodayLocal`). |

---

## 10. Fuera de Alcance / Diferido a v2 [PERSONALIZAR]

* [Listar explícitamente integraciones o módulos que NO entran en el MVP.]
