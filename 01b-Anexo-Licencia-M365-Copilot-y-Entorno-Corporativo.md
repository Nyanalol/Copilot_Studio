# Anexo 01b · Tu caso real: licencia de M365 Copilot en un tenant corporativo

> 📌 Anexo al [Fascículo 01](./01-Fundamentos-y-Entorno.md). Léelo si **no** tienes una *trial* de Copilot Studio, sino una **licencia de Microsoft 365 Copilot** dentro del tenant de tu empresa (el caso de Miguel en SoftwareONE).

## 🎯 Por qué este anexo

El Fascículo 01 asume una prueba gratuita de Copilot Studio en un tenant "limpio". Tu realidad es distinta:

- Tienes **Microsoft 365 Copilot** (no una licencia *standalone* de Copilot Studio).
- Trabajas en un **tenant corporativo gobernado**, con varios **entornos** ya creados (DEV/TEST/PROD de proyectos, default, etc.).

Esto afecta a **qué puedes hacer** y a **dónde debes trabajar**. Vamos a aclararlo.

---

## 1. M365 Copilot ≠ Copilot Studio (licencias)

Son productos distintos que comparten marca (lo vimos en el Fascículo 00):

| | Microsoft 365 Copilot | Copilot Studio (licencia/capacidad) |
|---|---|---|
| Qué es | El asistente de IA **dentro** de Word, Teams, Outlook, BizChat | La herramienta para **crear** agentes propios |
| Tú tienes | ✅ Esto | ❌ No directamente |

### ✅ Qué te permite tu licencia de M365 Copilot

- **Entrar en Copilot Studio y construir/personalizar agentes** (autoría completa: instrucciones, temas, conocimiento, acciones). Para **aprender todo el curso**, te sirve.
- Usar el **agent builder ligero** integrado en el chat de M365 Copilot.
- Crear **agentes declarativos** que **extienden M365 Copilot** y se apoyan en tus datos de M365 (Microsoft Graph): SharePoint, correo, Teams, etc.
- Publicar para uso **interno** en **M365 Copilot** y **Teams**.

### ⚠️ Qué NO cubre automáticamente (importante)

El consumo de un agente se mide en **"mensajes de Copilot Studio"**. Tu licencia de M365 Copilot **no** incluye ese consumo de forma general para:

- Agentes en **canales externos** (web pública de un e-commerce, **Direct Line** en una app).
- **Respuestas generativas** sobre fuentes **ajenas a M365** (un PDF de políticas, un sitio web, Dataverse, una API…).
- **Acciones autónomas** y orquestación avanzada.

Para eso, la organización necesita **capacidad de Copilot Studio**, que se habilita de dos formas (lo decide/configura tu **admin**):
1. **Pay-as-you-go** (consumo medido facturado a una **suscripción de Azure**).
2. **Packs de mensajes prepago** (capacity packs).

> 🧠 **Traducción para tu caso:** puedes **aprender y prototipar** AuroraBot con tu licencia, pero el **AuroraBot completo del curso** (web pública + Power Automate + Direct Line + RAG sobre PDFs) **consume capacidad que M365 Copilot no incluye**. Para desplegarlo "de verdad" (o incluso para pruebas intensivas), pide a tu admin que habilite **pay-as-you-go** o un **pack de mensajes** en tu entorno de DEV. Esto enlaza con la estimación de costes del [Fascículo 10](./10-Arquitectura-de-Implementacion-Real.md).

---

## 2. Qué entorno elegir (caso SoftwareONE)

En tu selector de entornos viste algo como:

```
Entornos admitidos (6)
  • Funding Factory - DEV
  • DACH Intern
  • Funding Factory - TEST
  • SWO-xDC-Warsaw-PROD
  • Services-ForeCastingApp-DEV
  • SWO-BusinessAutomation
Entorno predeterminado (1)
  • SoftwareONE (default)   ← seleccionado
```

### ⛔ Dónde NO trabajar

| Entorno | Por qué evitarlo |
|---|---|
| **SoftwareONE (default)** | Es el **predeterminado** del tenant: compartido por toda la organización, sin gobernanza propia y a menudo restringido. **Regla de oro: nunca el default.** |
| **SWO-xDC-Warsaw-PROD** | Es **producción**. No se experimenta en prod. Jamás. |
| **Funding Factory - DEV / TEST** | Entorno de **otro proyecto/equipo**. No es tuyo; puede que no tengas permisos de *maker* y ensuciarlo molesta a ese equipo. |
| **Services-ForeCastingApp-DEV** | Ídem: proyecto concreto ajeno. |
| **DACH Intern** | Entorno de un área concreta; mismo razonamiento. |
| **SWO-BusinessAutomation** | Probablemente automatizaciones corporativas en uso; no es un sandbox de aprendizaje. |

### ✅ Dónde SÍ trabajar (en orden de preferencia)

1. **Pide tu propio entorno DEV/sandbox** al **admin de Power Platform** de SoftwareONE:
   - Nombre tipo `SWO-CopilotStudio-Miguel-DEV` o `…-Learning`.
   - Con **Dataverse** habilitado (lo necesitarás en los Fascículos 04, 06, 07, 09).
   - Tú con rol **System Customizer** o **Maker** (y **Environment Maker** para crear).
   - Región **Europa** (RGPD / residencia de datos).
   - Si vas a probar canales externos o RAG intensivo, pide que te habiliten **capacidad de Copilot Studio** (pay-as-you-go) en ese entorno.
2. **Si no consigues entorno propio rápido:** usa el **Power Platform Developer Plan** en un **tenant personal gratuito** (Fascículo 01, Paso 1, opción C). Aprendes **sin restricciones**, aunque pierdes acceso a los datos M365 de SoftwareONE. Ideal para dominar el producto antes de llevarlo al tenant corporativo.

> 🔐 **Por qué importa tanto el entorno:** el entorno determina permisos, datos (Dataverse), políticas **DLP**, residencia de datos y a quién afecta lo que rompas. Trabajar en el sitio correcto es la diferencia entre "aprendo tranquilo" y "tiré algo de un proyecto en producción".

---

## 3. Cómo adaptar el curso a tu situación

| Fascículo | Adaptación para tu caso (M365 Copilot + tenant corporativo) |
|---|---|
| 01 | Usa **tu entorno DEV propio** (pedido al admin) o un **Developer Plan** personal. No el default. |
| 02–05 | Igual que el curso. La autoría (temas, entidades, conocimiento) funciona con tu licencia. Para RAG intensivo, vigila el **consumo de mensajes**. |
| 06–07 | Power Automate/conectores **premium** y APIs externas pueden requerir licencias/capacidad adicionales que gestiona el admin. En corporativo, ojo a las **políticas DLP** (pueden bloquear conectores). |
| 08 | Para uso **interno**, publica en **Teams/M365 Copilot** (cubierto). Para **web/Direct Line** (externo) necesitarás **capacidad de Copilot Studio** habilitada. |
| 09 | En corporativo, la **gobernanza ya existe** (DLP, entornos). Te conviene entenderla **con tu admin**, no crearla de cero. |
| 10 | Tu estimación de costes para un cliente debe distinguir lo que cubre **M365 Copilot** de lo que requiere **capacidad de Copilot Studio**. Justo el tipo de matiz que te pedirá un cliente. |

---

## 4. Acciones concretas para ti, ahora

- [ ] **Escribe a tu admin de Power Platform / IT** pidiendo un **entorno DEV propio** con Dataverse y tu rol de *maker* (plantilla abajo).
- [ ] Mientras llega, crea una cuenta de **Power Platform Developer Plan** para no quedarte parado.
- [ ] Pregunta al admin si hay **capacidad de Copilot Studio (pay-as-you-go o packs)** disponible, o cómo solicitarla.
- [ ] Confirma las **políticas DLP** del tenant (qué conectores puedes usar).

### ✉️ Plantilla de petición al admin

```
Asunto: Solicitud de entorno de desarrollo en Power Platform para Copilot Studio

Hola [admin],

Estoy formándome en Copilot Studio y desarrollando un agente de prueba.
¿Podrías crearme un entorno de DESARROLLO/sandbox propio?

- Nombre sugerido: SWO-CopilotStudio-[mi-nombre]-DEV
- Con base de datos Dataverse habilitada
- Región: Europa
- Mi rol: System Customizer / Environment Maker
- Si es posible, capacidad de Copilot Studio (pay-as-you-go o pack de mensajes)
  para poder probar respuestas generativas y canales.

Gracias,
[nombre]
```

---

## 📎 Referencias oficiales

- Licencias y suscripciones de Copilot Studio: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/requirements-licensing-subscriptions>
- Copilot Studio y M365 Copilot (qué incluye): <https://learn.microsoft.com/en-us/microsoft-copilot-studio/billing-licensing>
- Pago por consumo (pay-as-you-go): <https://learn.microsoft.com/en-us/microsoft-copilot-studio/requirements-messages-management>
- Agentes que extienden M365 Copilot: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/microsoft-copilot-extend-copilot-extensions>
- Gestión de entornos (admin): <https://learn.microsoft.com/en-us/power-platform/admin/create-environment>

---

⬅️ Volver al [Fascículo 01](./01-Fundamentos-y-Entorno.md) · o al [índice del curso](./README.md)
