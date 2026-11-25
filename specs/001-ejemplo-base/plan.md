# Plan Técnico — Spec 001 (Ejemplo Base)

> **Plantilla de referencia:** Muestra cómo bajar un `spec.md` a arquitectura `.NET 10` por capas sin invadir `spec.md` con detalles técnicos.

## 1. Arquitectura por Capas (.NET 10)
* **`Domain/`:** Entidad `EmailOutbox`, enum `EmailOutboxStatus` (`Pending`, `Sent`, `FailedPermanent`), entidad `GlobalParameter` y utilidades puras de redacción de PII.
* **`Application/`:** Interfaces `IEmailSender` y `ISensitiveDataProtector`, caso de uso de registro y servicio de encolado usando `TimeProvider`.
* **`Infrastructure/`:** `ApplicationDbContext` (EF Core / SQL Server, `.HasConversion<string>().HasMaxLength(40)`), cifrado `PayloadJson` vía ASP.NET Core Data Protection (`DataProtectionKeys` en BD), `CreateExecutionStrategy()`, `OutboxWorker` (`BackgroundService` cada 5 min + `Channel<Guid>` post-commit).
* **`Web/`:** Razor Pages `/cuenta/registro` y `/cuenta/login`, layout con meta CSRF y listener `htmx:configRequest`, rate limiting y estilos Tailwind CSS.

## 2. Modelo de Datos, Zona Horaria y Concurrencia
* Persistencia estricta de fechas en `UTC` mediante `TimeProvider`.
* Índice filtrado en `EmailOutbox (NextAttemptAtUtc) WHERE Status = 'Pending'`.
* Despacho idempotente mediante `ExecuteUpdateAsync` verificando `rowsAffected == 1`.

## 3. Trazabilidad de Decisiones
* **Outbox transaccional en BD vs envío directo en HTTP request:** Se elige `EmailOutbox` en la misma transacción para cubrir `RF-1` y `CA-01` sin bloquear el request del usuario ante caídas del proveedor de correo.

## 4. Estrategia de Verificación y Pruebas
* `UnitTests`: `EmailOutboxPolicyTests` con `FakeTimeProvider` verificando backoff exponencial y paso a `FailedPermanent` al 5.º fallo (`RF-3`).
* `IntegrationTests` (SQL Server real): `OutboxAndIdentityIntegrationTests` verificando cifrado en reposo de `PayloadJson` y migración EF Core (`CA-01`).
