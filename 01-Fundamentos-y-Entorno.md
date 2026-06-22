# Fascículo 01 · Fundamentos y entorno

> ⏱️ Duración estimada: 1 h · 🟢 Nivel: introductorio

## 🎯 Objetivos

- Entender los **conceptos clave** de Copilot Studio (agente, tema, entidad, variable, acción, canal).
- Comprender **licencias y costes** (importante para un cliente real).
- Saber qué es un **entorno (environment)** de Power Platform y por qué importa.
- **Acceder** a Copilot Studio (con prueba gratuita si hace falta) y dejar tu espacio listo.

---

## 📖 Concepto

### El vocabulario que vas a usar todo el curso

| Término | Qué es | Analogía e-commerce |
|---|---|---|
| **Agente** (agent/copilot) | El asistente conversacional completo | AuroraBot |
| **Tema** (topic) | Un flujo de conversación para un objetivo | "Seguimiento de pedido" |
| **Frase desencadenante** (trigger phrase) | Lo que el usuario dice para activar un tema | "¿dónde está mi pedido?" |
| **Nodo** (node) | Cada paso dentro de un tema | "Pregunta el nº de pedido" |
| **Entidad** (entity) | Un tipo de dato que el agente reconoce | nº de pedido, email, ciudad |
| **Variable** | Donde se guarda un dato durante la conversación | `Var.NumeroPedido` |
| **Acción** (action) | Algo que el agente *hace* (llamar a un sistema) | consultar el estado del pedido en el ERP |
| **Conocimiento** (knowledge) | Fuentes de las que el agente extrae respuestas | política de devoluciones (PDF/web) |
| **Canal** (channel) | Dónde vive el agente | web de Aurora, Teams, app |
| **Entorno** (environment) | Un contenedor aislado en Power Platform | Dev / Test / Prod |

### Licencias y costes (lo que un cliente te va a preguntar)

Esto es lo que la doc oficial de Learn apenas toca y es **crítico en un proyecto real**:

- **Copilot Studio se factura por consumo de mensajes** (modelo de *capacity packs*). Una conversación consume "mensajes"; las respuestas generativas y acciones consumen más que un nodo simple.
- Hay **dos formas de licenciar**:
  1. **Licencia independiente de Copilot Studio** (un pack mensual con una bolsa de mensajes; puedes añadir packs adicionales).
  2. **Incluido/derechos a través de Microsoft 365 Copilot** (en algunos planes se obtienen capacidades adicionales).
- Para **aprender** tienes opciones gratuitas:
  - **Prueba gratuita de Copilot Studio** (trial de ~30 días).
  - **Developer Plan de Power Platform** (entorno de desarrollo gratuito).
- **Costes asociados que se olvidan** en las estimaciones:
  - **Power Automate** premium si llamas a APIs externas con conectores premium.
  - **Dataverse** (almacenamiento) si guardas datos.
  - **Azure** si usas Direct Line, App Insights, o servicios externos.
  - **Microsoft Fabric** capacidad (F SKUs) si integras datos de Fabric.

> 💰 **Consejo de consultoría:** cuando dimensiones un cliente, estima **nº de conversaciones/mes × mensajes por conversación**. Una FAQ generativa simple ≈ pocos mensajes; un flujo que llama a 3 APIs y usa IA puede multiplicar el consumo. Más detalle de costes en el Fascículo 10.

### ¿Qué es un "entorno" y por qué te importa?

Un **entorno (environment)** es un contenedor aislado dentro de un *tenant* de Power Platform: tiene sus propios agentes, flujos, datos (Dataverse) y permisos. Sirve para separar:

- **Desarrollo** (donde trasteas) — usaremos este en el curso.
- **Pruebas/UAT** (donde valida el cliente).
- **Producción** (lo que ven los usuarios reales).

Mover un agente entre entornos = **ALM** (Application Lifecycle Management). Lo vemos en el Fascículo 09. Por ahora solo necesitas saber que **trabajarás en un entorno de desarrollo**.

---

## 🔧 Práctica paso a paso · Deja tu espacio listo

### Paso 1 · Consigue acceso

Tienes tres caminos (elige el que puedas):

- **A) Cuenta corporativa M365** con permisos de Power Platform → ve directo al Paso 2.
- **B) Prueba gratuita de Copilot Studio:**
  1. Ve a <https://copilotstudio.microsoft.com>.
  2. Inicia sesión con tu cuenta de trabajo/escuela, o crea una **prueba**.
  3. Si te pide, regístrate para el trial gratuito.
- **C) Power Platform Developer Plan** (gratis, ideal para aprender):
  1. Ve a <https://powerapps.microsoft.com/developerplan/>.
  2. Regístrate → te crea un **entorno de desarrollo** gratuito.

> ℹ️ Si tu organización bloquea los registros, pide a tu admin un entorno de **sandbox** o usa una cuenta personal/educativa para el trial.

### Paso 2 · Entra en Copilot Studio

1. Abre <https://copilotstudio.microsoft.com>.
2. Inicia sesión.
3. Arriba a la derecha verás el **selector de entorno** (Environment). Comprueba en qué entorno estás.

### Paso 3 · Elige/crea tu entorno de trabajo

1. Pulsa el **selector de entorno** (esquina superior derecha).
2. Si tienes uno de **desarrollo / sandbox**, selecciónalo. Si no, usa el que te dio el trial.
3. **Anota el nombre del entorno** — lo usarás en todos los fascículos.

> ⚠️ **Error común:** crear el agente en el entorno **Default** (predeterminado) del tenant corporativo. Evítalo: el Default suele estar compartido por toda la organización y sin gobernanza. Usa un entorno propio de **Dev**.

### Paso 4 · Familiarízate con la interfaz

Date una vuelta por el menú lateral izquierdo. Aunque varía, encontrarás secciones como:

- **Home / Inicio** — punto de partida.
- **Create / Crear** — crear un agente nuevo.
- **Agents / Agentes** — tus agentes.
- Dentro de un agente: **Overview, Topics, Knowledge, Actions, Activity/Analytics, Channels, Settings**.

No crees nada todavía: eso es el Fascículo 02.

---

## 🧪 Prueba

- [ ] Puedes iniciar sesión en <https://copilotstudio.microsoft.com> sin errores.
- [ ] Identificas el **selector de entorno** y sabes en cuál estás.
- [ ] Localizas el botón para **crear un agente** (sin pulsarlo aún).

---

## 💡 Más allá de la doc oficial

- **Región del entorno = residencia de datos.** Al crear un entorno eliges región (p. ej. Europa). Para un cliente con requisitos de **RGPD**, esto es decisivo. No se puede cambiar después fácilmente.
- **Naming convention desde el día 1.** En cliente real, nombra entornos y agentes con un estándar: `Aurora-DEV`, `Aurora-PROD`, `AuroraBot-Atencion`. Te ahorra caos en ALM.
- **Idioma del agente.** Copilot Studio es multi-idioma, pero el **idioma principal se fija al crear el agente** y condiciona el reconocimiento. Para Aurora Store usaremos **español**. Piensa el idioma antes de crear.
- **Geo de los modelos generativos.** Las respuestas generativas pueden procesarse en regiones concretas; relevante para cumplimiento. Revisa la doc de *data location* si el cliente es sensible.

---

## 📎 Referencias oficiales

- Licencias de Copilot Studio: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/requirements-licensing-subscriptions>
- Inquilinos y entornos (Power Platform): <https://learn.microsoft.com/en-us/power-platform/admin/environments-overview>
- Quickstart Copilot Studio: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/fundamentals-get-started>
- Power Platform Developer Plan: <https://powerapps.microsoft.com/developerplan/>

---

## ✅ Checklist de la sesión

- [ ] Conozco el vocabulario clave (agente, tema, entidad, variable, acción, canal, entorno).
- [ ] Entiendo el **modelo de licencias por mensajes** y los costes asociados.
- [ ] Sé qué es un **entorno** y por qué no usar el Default.
- [ ] Tengo **acceso a Copilot Studio** y sé en qué entorno trabajaré.

➡️ **Siguiente:** [Fascículo 02 · Crea tu primer agente](./02-Crear-tu-Primer-Agente.md)
