# Fascículo 02 · Crea tu primer agente (AuroraBot)

> ⏱️ Duración estimada: 1 h · 🟢 Nivel: introductorio

## 🎯 Objetivos

- Crear **AuroraBot** desde cero (los dos métodos: descripción con IA y manual).
- Entender la **anatomía** de un agente en la UI de Copilot Studio.
- Configurar **identidad, idioma e instrucciones** del agente.
- **Probarlo** en el panel de pruebas y entender qué hace por defecto.

---

## 📖 Concepto

### Dos formas de crear un agente

1. **Con descripción (asistida por IA)** — describes en lenguaje natural qué quieres y Copilot Studio genera un primer agente (nombre, instrucciones, incluso un tono). Rápido para empezar.
2. **Manualmente / en blanco** — empiezas vacío y lo construyes tú. Más control.

Usaremos la **vía con descripción** para arrancar rápido y luego lo afinamos a mano (que es lo que harás en un proyecto real).

### Anatomía de un agente

Cuando abres un agente verás (los nombres pueden variar ligeramente):

- **Overview / Resumen** — instrucciones del agente, conocimiento, herramientas (tools/actions) y disparadores en un vistazo. Es el "cerebro".
- **Topics / Temas** — los flujos de conversación.
- **Knowledge / Conocimiento** — fuentes para respuestas generativas.
- **Tools / Actions / Herramientas** — acciones que puede ejecutar.
- **Activity / Analytics** — métricas de uso.
- **Channels / Canales** — dónde se publica.
- **Settings / Configuración** — idioma, autenticación, IA generativa, seguridad.

### Las "Instrucciones" del agente (clave en la era generativa)

En el modo generativo, el agente tiene un campo de **Instructions** (instrucciones / *system prompt*). Es texto en lenguaje natural que define:

- **Quién es** ("Eres AuroraBot, el asistente de Aurora Store…").
- **Cómo se comporta** (tono, límites, qué NO debe hacer).
- **Cómo decide** (cuándo escalar a humano, cuándo usar una acción).

Esto es lo más parecido a "programar con palabras". Lo trabajaremos en detalle en el Fascículo 05, pero ya configuramos una base.

---

## 🔧 Práctica paso a paso · Crea AuroraBot

### Paso 1 · Crear el agente

1. En Copilot Studio, ve a **Create / Crear** → **New agent / Nuevo agente**.
2. Si te ofrece el **modo conversacional de creación**, descríbelo. Pega algo como:

   ```
   Quiero un asistente de atención al cliente para Aurora Store, un e-commerce
   de electrónica. Debe ayudar con seguimiento de pedidos, devoluciones,
   preguntas sobre productos y políticas de envío, recomendar productos y
   escalar a un agente humano cuando no pueda resolver. Tono cercano,
   profesional y resolutivo. Idioma: español.
   ```

3. Cuando te pregunte detalles (nombre, tono), confirma o ajusta. Si te deja, ponle:
   - **Nombre:** `AuroraBot`
   - **Idioma:** Español (España) — *verifica que quede en español*.
4. Pulsa **Create / Crear**. Espera a que se genere.

> 🧭 Si tu UI te lleva primero a un formulario "en blanco", elige **Skip to configure / Configurar** y rellena Nombre = `AuroraBot`, Idioma = Español, y una descripción breve. Da igual el método: llegas al mismo sitio.

### Paso 2 · Revisa la pantalla Overview

Mira el resumen del agente. Probablemente Copilot Studio ya ha:

- Generado unas **instrucciones** iniciales.
- Quizá añadido **conocimiento** o temas de ejemplo.

No pasa nada si está "vacío": lo llenaremos nosotros.

### Paso 3 · Ajusta las instrucciones del agente

1. Abre **Overview** → sección **Instructions** (o **Settings → Generative AI / Instructions**).
2. Sustituye/define algo como:

   ```
   Eres AuroraBot, el asistente virtual de atención al cliente de Aurora Store,
   un e-commerce de electrónica.

   Tu objetivo es resolver dudas de clientes de forma rápida, clara y amable.

   Reglas:
   - Responde siempre en español, con tono cercano y profesional.
   - Si el cliente pregunta por el estado de un pedido, pide el número de pedido
     antes de consultar.
   - Para devoluciones, guía paso a paso según la política de Aurora Store.
   - Si no tienes información suficiente o el cliente está enfadado o pide
     hablar con una persona, ofrece escalar a un agente humano.
   - No inventes datos de pedidos, precios ni stock: usa solo la información
     disponible en las fuentes de conocimiento y las acciones.
   - No compartas información personal de otros clientes.
   ```

3. **Guarda** (Save).

### Paso 4 · Configura el idioma (verificación)

1. Ve a **Settings / Configuración** → **Languages / Idiomas** (o **General**).
2. Confirma que el **idioma principal es Español**.

> ⚠️ El idioma principal **no se puede cambiar** una vez creado el agente en muchos casos. Si quedó en inglés, considera **recrear** el agente en español. Mejor ahora que con 20 temas hechos.

### Paso 5 · Pruébalo

1. Abre el **panel de pruebas** (Test / Probar, normalmente a la derecha).
2. Escribe: `Hola`
3. Prueba: `¿Cuál es vuestra política de devoluciones?`
4. Observa cómo responde. Sin conocimiento aún, dará una respuesta genérica o dirá que no sabe. **Es normal**: aún no le hemos dado temas ni conocimiento.

---

## 🧪 Prueba

- [ ] AuroraBot existe y aparece en tu lista de agentes.
- [ ] El idioma principal es **Español**.
- [ ] Las **instrucciones** reflejan el rol de atención al cliente de Aurora Store.
- [ ] Responde algo en el panel de pruebas (aunque sea genérico).

---

## 💡 Más allá de la doc oficial

- **Las instrucciones son tu activo más valioso.** En proyectos reales, el 70% de la "personalidad" y fiabilidad del agente sale de unas instrucciones bien escritas. Sé explícito con lo que **NO** debe hacer (alucinaciones de precio/stock son el error nº1 en e-commerce).
- **Activa/revisa "Generative AI" en Settings.** Hay un modo de **orquestación generativa** (el agente decide qué tema/acción usar) y un modo **clásico** (solo dispara temas por frases). Para aprender los flujos del Fascículo 03–04 viene bien tener el control; activaremos la orquestación generativa en el Fascículo 05. Apunta dónde está esa opción: **Settings → Generative AI / Orchestration**.
- **System Topics ocultos.** Todo agente trae **temas de sistema** (Greeting, Goodbye, Escalate, Fallback, Conversation Start, Reset…). No los borres; los personalizaremos. El **Fallback** (qué hacer cuando no entiende) es clave para la experiencia.
- **Versiona desde el principio.** Cada vez que guardas, hay un estado. Antes de cambios grandes, **publica** (crea un punto estable) o exporta una **solución** (Fascículo 09). Evita "se me rompió y no sé qué toqué".

---

## 📎 Referencias oficiales

- Crear y configurar un agente: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/fundamentals-get-started>
- Instrucciones del agente: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-add-instructions>
- Temas de sistema: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-system-topics>

---

## ✅ Checklist de la sesión

- [ ] He creado **AuroraBot** en mi entorno de desarrollo.
- [ ] El idioma es español y las instrucciones definen su rol y límites.
- [ ] Sé dónde están Topics, Knowledge, Actions, Channels, Settings.
- [ ] He probado el agente en el panel de pruebas.

➡️ **Siguiente:** [Fascículo 03 · Temas y flujos de conversación](./03-Temas-y-Flujos-de-Conversacion.md)
