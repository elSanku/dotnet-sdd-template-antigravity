# Tareas — Spec 001 (Ejemplo Base)

- [ ] **T1. Crear solución .NET 10 por capas y entidades base de Dominio (`EmailOutbox`, `GlobalParameter`).** RF-1, RF-3 (`RN-01`)
  - Hecho cuando: `dotnet build` compila sin errores y `Domain` tiene cero referencias a EF Core o Web.
- [ ] **T2. Configurar `ApplicationDbContext`, Identity, Data Protection e índice filtrado en SQL Server.** RF-1, RNF-2 (`RN-01`)
  - Hecho cuando: migración inicial aplica sobre SQL Server real y test de cifrado de `PayloadJson` pasa en `IntegrationTests`.
- [ ] **T3. Implementar registro en Razor Pages con antiforgery HTMX, rate limiting y encolado en `EmailOutbox`.** RF-1, RF-2, RNF-1, RNF-3 (`RN-01`)
  - Hecho cuando: registro válido crea usuario + outbox en una transacción y correo duplicado devuelve error en español.
- [ ] **T4. Implementar `OutboxWorker` con `TimeProvider`, backoff exponencial y pruebas xUnit.** RF-3 (`CA-01`)
  - Hecho cuando: `dotnet test` pasa tests unitarios con `FakeTimeProvider` e integración en SQL Server real (`CA-01`).
