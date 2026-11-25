# AGENTS.md — [PERSONALIZAR: <Nombre del Proyecto>]
[PERSONALIZAR: Descripción breve del sistema, propósito de negocio y stack — ej. Plataforma web monolítica modular en .NET 10 LTS (Razor Pages + HTMX + Tailwind CSS + EF Core / SQL Server) con trazabilidad SDD.]

## Stack y estructura
- .NET 10 LTS (`net10.0`), ASP.NET Core **Razor Pages + HTMX** (Page Handlers y parciales `_Partial.cshtml`), Tailwind CSS, EF Core (Code-First) sobre **SQL Server** y xUnit. *(Ajustar aquí si el proyecto incluye Minimal APIs o Worker independiente).*
- Prohibido introducir MVC Controllers de vistas, Blazor o SPAs externas (React, Angular, Vue, Vite) salvo que el SSOT indique lo contrario.
- Arquitectura en capas:
  - `src/<NamespaceBase>.Domain`: Entidades, enums cerrados, invariantes puros y validadores (cero dependencias a EF Core o Web).
  - `src/<NamespaceBase>.Application`: Casos de uso / comandos con `TimeProvider`, contratos de infraestructura (`IEmailSender`, etc.) y validaciones.
  - `src/<NamespaceBase>.Infrastructure`: `ApplicationDbContext` (EF Core / SQL Server), transacciones con bloqueos pesimistas, cifrado Data Protection, clientes externos y `BackgroundService` idempotentes.
  - `src/<NamespaceBase>.Web`: Razor Pages, handlers HTMX, antiforgery global, rate limiting, filtros Anti-IDOR y Tailwind CSS.
  - `tests/<NamespaceBase>.UnitTests`: Pruebas unitarias xUnit con `FakeTimeProvider`.
  - `tests/<NamespaceBase>.IntegrationTests`: Pruebas de integración y concurrencia xUnit contra **SQL Server real** (LocalDB o Testcontainers).

## Convenciones
- Toda interacción con base de datos se realiza mediante `ApplicationDbContext` desde `Infrastructure`, usando SQL crudo parametrizado únicamente para bloqueos pesimistas (`UPDLOCK, ROWLOCK`) y transiciones atómicas (`ExecuteUpdateAsync` verificando `rowsAffected == 1`).
- Cero referencias a EF Core, SQL o Web en `Domain`. Prohibido instalar ORMs alternativos o usar `InMemory`/`SQLite` para probar concurrencia e índices filtrados.
- Uso obligatorio de `TimeProvider` en todas las capas (prohibido `DateTime.UtcNow`, `DateTime.Now` o `DateTimeOffset.UtcNow` directos).
- Enums persistidos en SQL Server con `.HasConversion<string>().HasMaxLength(40)`.
- Código simple, modular y sin abstracciones especulativas (disciplina Ponytail); comentarios solo donde aporten contexto del porqué de negocio.
- Delimitar todo bloque de código nuevo o editado con marcas de auditoría unificadas: `//INI SPEC-XXX` y `//FIN SPEC-XXX` (en vistas `.cshtml`: `@* INI SPEC-XXX *@` y `@* FIN SPEC-XXX *@`, sin sufijo `- Tn`).
- Regla de auditoría en modificaciones: en archivos preexistentes, NUNCA sobrescribir ni borrar marcas históricas (`//INI SPEC-00X`); preservar el bloque original y delimitar quirúrgicamente solo las líneas modificadas con un comentario explicativo breve.
- Interfaz de usuario, validaciones, logs funcionales, correos y documentación 100% en **español**.
- Redacción obligatoria de PII en logs: nunca registrar contraseñas, tokens, documentos de identidad completos ni números de cuenta bancaria sin enmascarar (`****1234`).

## Datos
- **[PERSONALIZAR: Catálogo e Identidad (`ApplicationDbContext`)]**:
  - `ApplicationUser` (hereda de `IdentityUser`, roles exclusivos en `AspNetUserRoles`, sin columna `Role` duplicada).
  - `[PERSONALIZAR: EntidadPrincipalA]`, `[PERSONALIZAR: EntidadCatalogoB]`, `GlobalParameter`.
- **[PERSONALIZAR: Transaccional / Negocio]**:
  - `[PERSONALIZAR: EntidadTransaccional]` (con `RowVersion` e índices únicos filtrados donde aplique).
- **Auditoría, mensajería y seguridad**:
  - `[PERSONALIZAR: DomainAuditLog]` / `AdminAuditLog` (inmutables, solo `INSERT`).
  - `EmailOutbox` (cifrado en reposo, reintentos con backoff + despacho post-commit).
  - `DataProtectionKeys` en BD para cifrado de campos sensibles (`ValueConverter`) y supervivencia ante reinicios.

## Sincronización y Transaccionalidad (fácil equivocarse)
- **Zona horaria y DST (`[PERSONALIZAR: America/Santiago]`):** Persistir todo instante en `UTC` (`DateTime`) y fechas calendario puras en `DateOnly`. Evaluar "hoy" local siempre con `ConvertTimeFromUtc`. Al convertir hora local → UTC, manejar explícitamente `IsInvalidTime` y `IsAmbiguousTime` (`Max()` offset).
- **Workers inmunes a DST:** Todo `BackgroundService` diario corre por tick periódico (ej. cada 15 min + al arrancar) validando `LastRunDate < TodayLocal` en lugar de depender de una hora exacta fija como `00:05`. Cada sub-tarea corre en su propio `IServiceScope` y `try/catch` aislado.
- **Reintentos y orden fijo de locks:** Toda transacción manual con `BeginTransactionAsync` debe envolverse en `CreateExecutionStrategy()`. Adquirir `WITH (UPDLOCK, ROWLOCK)` invariablemente en el orden fijo definido en `docs/especificacion_tecnica_ia.md`: `[PERSONALIZAR: 1.º EntidadA → 2.º ApplicationUser → 3.º EntidadTransaccional]`.
- **Transiciones atómicas e idempotencia:** Ejecutar `ExecuteUpdateAsync(... WHERE Id = @id AND Status = @estadoEsperado)` verificando `rowsAffected == 1`. Si es `0`, abortar con conflicto (HTTP 409) o salir idempotentemente en workers.
- **Seguridad Web:** Antiforgery global en toda mutación HTMX (`htmx:configRequest`), validación Anti-IDOR por propietario/rol en todo handler y sanitización XSS en contenido generado por usuarios.

## Forma de trabajar
- Seguir estrictamente el flujo SDD por fases (`SPEC-001`…`SPEC-NNN`): `spec.md` → `clarify` → `plan.md` → `tasks.md` → `implement` (una sola tarea por turno) → `validate`.
- No inventar reglas, estados ni tablas fuera de `docs/especificacion_tecnica_ia.md`; ante un vacío genuino, detenerse y consultar.
- Cambios pequeños y enfocados; no romper la compilación ni adelantar lógica de fases futuras.
- Al terminar cada tarea, resumir qué se cambió, qué decisiones se tomaron y qué IDs (`RN-xx`, `TR-xx`, `CA-xx`) quedaron cubiertos.
- Al finalizar e implementar cada spec (antes del commit final), generar obligatoriamente el informe técnico en `docs/` en formatos `.md` y `.html`, redactado de forma didáctica para desarrolladores junior.

## Memoria
- Al empezar cada sesión, lee `MEMORY.md` para conocer la fase actual y las decisiones consolidadas.
- Al terminar una tarea o fase, actualiza `MEMORY.md`: estado actual, decisiones clave y errores a evitar.
- Mantenlo breve (máximo ~50–80 líneas): resume o elimina lo que ya no aporte.
- Si algo se convierte en regla permanente, propón moverlo a `docs/constitution.md` o a `AGENTS.md`.
- No guardes nunca contraseñas reales, claves de API ni datos personales reales.

## Comandos
- Compilación: `dotnet build`
- Pruebas (unitarias e integración SQL Server): `dotnet test`
- Ejecución local: `dotnet run --project src/<NamespaceBase>.Web`
- Nueva migración EF Core: `dotnet ef migrations add <Nombre> --project src/<NamespaceBase>.Infrastructure --startup-project src/<NamespaceBase>.Web`

## Reglas
- Lee `docs/constitution.md`, `docs/especificacion_tecnica_ia.md`, `MEMORY.md` y la spec activa (`specs/NNN-*/`) antes de tocar código.

## Límites
- ✅ Siempre: usar `TimeProvider`, `CreateExecutionStrategy()`, orden fijo de locks y verificar `rowsAffected == 1` en transiciones.
- ✅ Siempre: aplicar antiforgery en HTMX, validación Anti-IDOR, sanitización XSS, cifrado Data Protection en datos sensibles y redacción de PII en logs.
- ✅ Siempre: delimitar bloques de código nuevos o editados con `//INI SPEC-XXX` y `//FIN SPEC-XXX`, preservando intactas las marcas de specs anteriores.
- ✅ Siempre: actualizar `tasks.md` y `MEMORY.md` al terminar cada tarea, y generar el informe técnico `.md` y `.html` en `docs/` al cerrar cada spec.
- ⚠️ Pregunta antes: si aparece un caso no cubierto por `docs/especificacion_tecnica_ia.md` o si se requiere modificar el modelo de datos acordado.
- 🚫 Nunca: usar `DateTime.UtcNow`/`DateTime.Now` directo, `InMemory`/`SQLite` para probar concurrencia ni guardar binarios pesados en BD o disco sin autorización explícita.
- 🚫 Nunca: introducir MVC Controllers de vistas, Blazor o SPAs (React/Angular/Vue) si el stack definido es Razor Pages + HTMX.
- 🚫 Nunca: sobrescribir o borrar marcas `//INI SPEC-00X` previas al modificar un archivo existente en una spec nueva.
- 🚫 Nunca: registrar datos sensibles sin enmascarar en logs o `LastError`, ni tragar excepciones en workers.

## Verificación
- Compilación limpia con `dotnet build` sin errores.
- Suite xUnit (`dotnet test`) en verde nombrando cada test con el ID (`RN-xx`, `TR-xx`, `CA-xx`), cubriendo como mínimo por cada módulo:
  1. **Unitarias (`FakeTimeProvider`):** reglas de negocio, máquina de estados, DST (`CA-01`), cálculos/redondeos y validadores.
  2. **Integración (SQL Server real — LocalDB/Testcontainers):** serialización con `UPDLOCK, ROWLOCK`, índices únicos filtrados, `ExecuteUpdateAsync` y cifrado en reposo.
