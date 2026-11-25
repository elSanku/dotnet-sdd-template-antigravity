# Spec 001 — Ejemplo Base de Fundación, Identidad y Outbox (SPEC-001)

> **Plantilla de referencia:** Usa este archivo como modelo de estructura para tus futuras specs y elimínalo o archívalo cuando empieces un proyecto real.

Estado: borrador
Reglas SSOT cubiertas: RN-01, TR-01, CA-01

## Contexto y objetivo
Establecer la base técnica del proyecto con identidad segura, almacenamiento persistente de parámetros operativos y mensajería transaccional confiable antes de construir los módulos funcionales.

## Usuarios
* **Usuario estándar (`User`):** Se registra, verifica su correo y accede a su panel.
* **Administrador (`Admin`):** Gestiona parámetros globales del sistema.

## Historias de usuario
* **HU-1.** Como usuario nuevo, quiero registrarme con correo único y aceptar los términos vigentes para operar de forma segura en la plataforma.
* **HU-2.** Como sistema, quiero encolar correos dentro de la misma transacción de negocio para garantizar que ninguna notificación crítica se pierda ante fallos temporales de red.

## Requisitos funcionales (EARS)
* **RF-1 (`RN-01`):** CUANDO un visitante envía el formulario de registro con datos válidos y aceptación de términos, EL SISTEMA creará la cuenta en el rol `User`, grabará la marca temporal `UTC` de consentimiento y encolará el correo de verificación en el mismo commit.
* **RF-2 (`RN-01`):** SI el correo electrónico ya existe en el sistema, ENTONCES EL SISTEMA rechazará el registro mostrando un mensaje claro en español sin revelar detalles internos.
* **RF-3 (`TR-01`):** CUANDO exista un correo pendiente cuyo próximo intento haya vencido, EL SISTEMA intentará despacharlo y actualizará su estado de forma atómica con reintentos exponenciales hasta un máximo de 5 intentos.

## Requisitos no funcionales
* **RNF-1 (Seguridad Web):** Toda mutación desde la interfaz debe validar token antiforgery (incluyendo peticiones HTMX) y aplicar limitación de tasa en login/registro.
* **RNF-2 (Privacidad y Logs):** Los payloads de correo almacenados deben cifrarse en reposo y los registros de error deben ocultar datos personales sensibles.
* **RNF-3 (Idioma y Accesibilidad):** Toda etiqueta, mensaje de validación y botón debe estar 100% en español cumpliendo contraste WCAG AA.

## Casos límite y Criterios de Aceptación (Given / When / Then)
* **`CA-01` (`RN-01`):** *Dado* un fallo simulado del proveedor SMTP al registrarse un usuario, *cuando* se completa el registro, *entonces* el usuario y su correo en `Pending` quedan persistidos y el despacho se reintenta automáticamente sin pérdida.

## Fuera de alcance
* Flujos de negocio específicos de fases posteriores (`SPEC-002` en adelante).

## Criterios de finalización (Definition of Done)
* Migración inicial aplicada limpiamente sobre SQL Server real.
* Compilación limpia (`dotnet build`) y pruebas xUnit (`dotnet test`) en verde cubriendo `RN-01` y `CA-01`.

## Dudas abiertas
* *(Ninguna duda abierta en este ejemplo).*
