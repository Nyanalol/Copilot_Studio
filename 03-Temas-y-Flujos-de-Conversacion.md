# Fascículo 03 · Temas y flujos de conversación

> ⏱️ Duración estimada: 1,5 h · 🟡 Nivel: intermedio

## 🎯 Objetivos

- Entender qué es un **tema (topic)** y su anatomía (triggers + nodos).
- Conocer los **tipos de nodo** (mensaje, pregunta, condición, redirección, acción…).
- Construir el tema **"Seguimiento de pedido"** con ramas.
- Configurar el **escalado a humano** y el **fallback**.

---

## 📖 Concepto

### ¿Qué es un tema?

Un **tema (topic)** es un flujo de conversación diseñado para cumplir **un objetivo concreto**. Se compone de:

1. **Trigger (desencadenante):** qué hace que el tema se active. Puede ser:
   - **Frases de ejemplo** (trigger phrases): el clásico. Das 5–10 ejemplos de cómo el usuario lo pediría y el agente generaliza.
   - **Disparador generativo / por descripción:** describes en lenguaje natural cuándo usarlo y el LLM decide (orquestación generativa, Fascículo 05).
2. **Nodos (nodes):** los pasos del flujo, conectados como un diagrama.

### Tipos de nodo (los que más usarás)

| Nodo | Para qué |
|---|---|
| **Send a message / Mensaje** | El agente dice algo |
| **Ask a question / Pregunta** | Pide un dato al usuario (y lo guarda en variable) |
| **Add a condition / Condición** | Bifurca según una variable (if/else) |
| **Topic management / Redirect** | Salta a otro tema |
| **Call an action / Acción** | Ejecuta un flujo, conector o API |
| **Variable management / Set variable** | Asigna valores |
| **Question with adaptive card** | Pregunta con tarjeta visual (botones, listas) |
| **Transfer / Escalate** | Pasa a un agente humano |
| **End of conversation** | Cierra y pide feedback |

### Variables y ámbito

- Una variable creada en un tema es, por defecto, **de tema** (vive mientras dure ese flujo).
- Puedes hacerla **global** (`Global.`) para compartirla entre temas (p. ej., el email del cliente una vez identificado).
- Las variables de sistema (`System.`) traen info del contexto (canal, idioma, actividad…).

Profundizamos en variables y entidades en el Fascículo 04. Aquí las usamos a nivel básico.

---

## 🔧 Práctica paso a paso · Tema "Seguimiento de pedido"

> En este fascículo construimos la **versión sin conexión a sistemas** (simulada). En el Fascículo 06 la conectaremos a un sistema real con una acción.

### Paso 1 · Crear el tema

1. En AuroraBot, ve a **Topics / Temas**.
2. **+ Add a topic / Nuevo tema** → **From blank / En blanco**.
3. Nómbralo: `Seguimiento de pedido`.

### Paso 2 · Definir el desencadenador (trigger)

> 🆕 **Lo que verás en la UI actual (orquestación generativa activada por defecto).** En los agentes nuevos, el desencadenador aparece como **"El agente elige"** y te pide una **descripción** ("Describe lo que hace el tema") en vez de frases de ejemplo. Es el modo moderno: el agente decide cuándo activar el tema **leyendo esa descripción**. **Recomendado: quédate en este modo.** Pega una descripción clara:
>
> ```
> Este tema ayuda al cliente a consultar el estado y la ubicación de su pedido
> (seguimiento o tracking de un envío de Aurora Store). Actívalo cuando el
> usuario pregunte dónde está su pedido, cuándo llegará su paquete, quiera
> rastrear un envío, diga que su pedido no ha llegado o pida el estado de su
> compra.
> ```
>
> La **descripción es el "mando" de la orquestación**: cuanto más clara y específica, mejor decide el agente.

**Modo clásico (alternativa, solo si tu agente NO tiene orquestación generativa):** el desencadenador te pedirá **frases de ejemplo (trigger phrases)**. En ese caso añade frases como:

```
¿dónde está mi pedido?
seguimiento de pedido
quiero saber el estado de mi compra
mi pedido no ha llegado
rastrear pedido
cuándo llega mi paquete
estado de mi envío
```

> 💡 En modo clásico, da variedad (formal/informal, con y sin tildes). El modelo generaliza, pero ejemplos diversos = mejor reconocimiento. Puedes cambiar el tipo de desencadenador pulsando **✏️ Editar** sobre "El agente elige".

### Paso 3 · Pedir el número de pedido

1. Añade un nodo **Ask a question / Pregunta**.
2. Mensaje: `¡Claro! Para localizar tu pedido, ¿me indicas tu número de pedido? (Lo encuentras en el email de confirmación, ej.: AUR-100245)`
3. **Identificar / Identify:** el campo aparece por defecto como **"Opciones de tipo test"** (pregunta de botones). ⚠️ Cámbialo a **"Respuesta completa del usuario"** (*User's entire response*) para capturar el número que el cliente escribe libremente. Al hacerlo desaparece la sección "Opciones para el usuario". En el Fascículo 04 lo cambiaremos por una **entidad personalizada** que valide el formato.
4. **Guardar respuesta del usuario como:** renombra la variable (sale como `Var1`, tipo `choice`) a **`NumeroPedido`**. Al cambiar el "Identificar" a respuesta completa, su tipo pasa a **texto (string)**.

### Paso 4 · Simular la consulta del estado (rama con condición)

Como aún no tenemos sistema real, simulamos con una condición sobre el número:

1. Añade un nodo **Add a condition / Condición**. Crea **3 ramas** (al añadir condiciones se genera la rama *"Todas las demás condiciones"* automáticamente).
2. Define cada rama según `NumeroPedido`:
   - **Rama 1:** `NumeroPedido` **es igual a** `AUR-100245` → Mensaje:
     `📦 Tu pedido {NumeroPedido} está EN REPARTO y se entregará hoy antes de las 20:00. Transportista: AuroraExpress. ¿Necesitas algo más?`
   - **Rama 2:** `NumeroPedido` **es igual a** `AUR-200111` → Mensaje:
     `🚚 Tu pedido {NumeroPedido} ha sido ENVIADO y llegará en 2-3 días laborables.`
   - **Rama "Todas las demás condiciones" (Else):** Mensaje:
     `No he podido localizar el pedido {NumeroPedido}. ¿Puedes verificar el número? Si crees que es correcto, te paso con un agente.`

> ⚠️ **Operador:** para variables de **texto**, Copilot Studio **no ofrece "contiene"** en el desplegable (solo `es igual a`, `no es igual a`, `está en blanco`, `en`…). Por eso usamos **`es igual a`** con el número exacto. El operador `en` significa "está dentro de" y va en sentido inverso: **no lo uses** aquí.
>
> ⚠️ **Valor como texto, no fórmula:** asegúrate de que el valor sea **texto plano** (`AUR-100245`). Si ves el icono `fx` (Power Fx) en el campo de valor, cámbialo a valor literal; si no, la condición fallará.
>
> ℹ️ **Insertar la variable** en un mensaje: teclea `{` o usa el icono `{x}` y elige `NumeroPedido` (no escribas las llaves a mano).
>
> 🔎 **Consecuencia de la simulación:** al ser coincidencia **exacta**, solo funcionarán esos dos números. Es intencionado y didáctico: demuestra por qué en producción esto se sustituye por una **acción real** (Fascículo 06) que consulta *cualquier* pedido.

### Paso 5 · Ofrecer escalado en la rama de error

Dentro de la rama "Else":

1. Tras el mensaje, añade una **Pregunta** con opciones (botones): `¿Quieres que te pase con un agente?` → opciones **Sí** / **No**.
2. Si **Sí** → añade nodo **Transfer conversation / Escalate** (escalado a humano).
3. Si **No** → Mensaje de cierre amable + nodo **End of conversation**.

### Paso 6 · Cierre del tema

Al final de las ramas de éxito, añade:

1. Una **Pregunta**: `¿Te puedo ayudar en algo más?` (Sí/No).
2. Si **No** → **End of conversation** (esto dispara la encuesta de satisfacción).

### Paso 7 · Personaliza Greeting, Fallback y Escalate (temas de sistema)

1. Ve a **Topics → System topics**.
2. **Conversation Start / Greeting:** pon un saludo de marca:
   `¡Hola! Soy AuroraBot 🤖, el asistente de Aurora Store. Puedo ayudarte con pedidos, devoluciones y dudas sobre productos. ¿Qué necesitas?`
3. **Fallback** (cuando no entiende): que sea útil, no un callejón:
   `Mmm, no estoy seguro de haberte entendido. Puedo ayudarte con: 📦 seguimiento de pedidos, 🔄 devoluciones, ❓ dudas de productos. ¿Sobre cuál quieres?`
   Y ofrece escalar tras 2 intentos fallidos.
4. **Escalate:** mensaje claro de traspaso:
   `Te paso con un agente del equipo de Aurora Store. Un momento, por favor… 🙋`

---

## 🧪 Prueba

En el panel de pruebas (recarga con **Reset** tras cada cambio):

1. `¿dónde está mi pedido?` → debe pedir el número.
2. Responde `AUR-100245` → debe decir "EN REPARTO".
3. Responde `AUR-200111` → debe decir "ENVIADO".
4. Responde `AUR-999` → debe ofrecer escalar a un agente.
5. Escribe algo sin sentido (`asdfgh`) → debe entrar el **Fallback**.

- [ ] Las 5 pruebas se comportan como se espera.

---

## 💡 Más allá de la doc oficial

- **Trigger phrases vs. orquestación generativa.** En el modo clásico, si dos temas tienen frases parecidas, compiten y a veces gana el equivocado. La **orquestación generativa** (Fascículo 05) resuelve mucho esto usando la **descripción** del tema en lugar de solo frases. En cliente real se suele acabar usando descripciones bien escritas + frases.
- **No metas lógica de negocio en condiciones a mano.** La simulación con `contiene 100` es solo didáctica. En producción **nunca** decidas el estado del pedido con reglas hardcodeadas: llama a una **acción/API** (Fascículo 06). Lo hacemos así ahora solo para aprender el flujo.
- **Cuidado con los "callejones sin salida".** Todo nodo final debe dejar al usuario una salida (seguir, escalar o cerrar). Un Fallback que solo dice "no entiendo" es la causa nº1 de frustración.
- **Usa Adaptive Cards para opciones.** Cuando hay opciones cerradas (Sí/No, motivos de devolución), una pregunta con botones reduce errores de comprensión frente a texto libre.
- **Variables globales para identidad.** Una vez identificas al cliente (email/pedido), guárdalo en `Global.` para no volver a pedirlo en cada tema.
- **Comentarios y orden.** Renombra los nodos y usa nombres de variable claros (`NumeroPedido`, no `Var1`). Tu yo del futuro lo agradecerá al depurar.

---

## 📎 Referencias oficiales

- Crear y editar temas: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-create-edit-topics>
- Tipos de nodo: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-node-types-overview>
- Temas de sistema: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-system-topics>
- Escalado / transferencia: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/advanced-hand-off>

---

## ✅ Checklist de la sesión

- [ ] Entiendo la anatomía de un tema (trigger + nodos).
- [ ] He creado el tema **Seguimiento de pedido** con ramas y escalado.
- [ ] He personalizado Greeting, Fallback y Escalate.
- [ ] Las pruebas pasan.

➡️ **Siguiente:** [Fascículo 04 · Entidades, variables y slot filling](./04-Entidades-Variables-y-Slot-Filling.md)
