---
name: sdd-constitution
description: Revisa y mantiene la constitución del proyecto (principios innegociables en docs/especificacion_tecnica_ia.md y docs/constitution.md).
---

# Skill: SDD-Constitution

Asegura la coherencia de los principios innegociables del proyecto entre `docs/constitution.md`, `AGENTS.md` y `docs/especificacion_tecnica_ia.md`:
1. **Spec-First y Trazabilidad**: Todo cambio nace de `specs/NNN-nombre/` (`SPEC-001`…`SPEC-NNN`) referenciando `RN-xx`, `TR-xx` y `CA-xx`, sin dependencias hacia fases futuras.
2. **100% Español**: Interfaz de usuario, mensajes, botones, correos transaccionales y documentación en español.
3. **Stack y Reglas Técnicas .NET 10**: C# / ASP.NET Core (.NET 10 LTS), **Razor Pages + HTMX** (con antiforgery global), EF Core / SQL Server (`HasConversion<string>()`, `CreateExecutionStrategy()`, `ExecuteUpdateAsync`), `TimeProvider` obligatorio (prohibido `DateTime.UtcNow`), Tailwind CSS, xUnit con **SQL Server real** en integración (prohibido `InMemory`/`SQLite` para concurrencia) y `BackgroundService` idempotentes e inmunes a DST.
4. **Invariantes de Concurrencia y Horarios**: Fechas persistidas en `UTC` (`DateTime`) y fechas puras en `DateOnly`; manejo determinista de DST; orden fijo de locks `UPDLOCK, ROWLOCK` y transiciones atómicas verificando `rowsAffected == 1`.
5. **Seguridad y Privacidad**: Cifrado en reposo Data Protection para datos sensibles y `EmailOutbox.PayloadJson`; redacción de PII en logs; sanitización XSS y validación Anti-IDOR.
6. **Anti-UI Slop y Accesibilidad**: Diseño intencional con Tailwind CSS, tipografía cuidada, contraste WCAG AA y navegación por teclado.
7. **Auditoría en Código**: Delimitación unificada con `//INI SPEC-XXX` y `//FIN SPEC-XXX` (sin sufijo `- Tn`), preservando marcas previas intactas.
