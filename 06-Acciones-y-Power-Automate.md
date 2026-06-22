# Fascículo 06 · Acciones y Power Automate

> ⏱️ Duración estimada: 2 h · 🔴 Nivel: avanzado

## 🎯 Objetivos

- Entender qué es una **acción (action / tool)** y los tipos disponibles.
- Crear un **flujo de Power Automate** que el agente invoque.
- Pasar **inputs** del agente al flujo y recibir **outputs** estructurados.
- Conectar de verdad el tema **"Seguimiento de pedido"** a una fuente de datos.

---

## 📖 Concepto

### ¿Qué es una acción?

Una **acción** (en la UI moderna, también **tool / herramienta**) es algo que el agente **ejecuta** para interactuar con el mundo exterior: consultar un pedido, crear un ticket, comprobar stock. Es lo que diferencia un agente que *informa* de uno que *hace*.

Tipos de acción en Copilot Studio:

| Tipo | Qué es | Cuándo usarlo |
|---|---|---|
| **Flujo de Power Automate** | Un flujo low-code con cientos de conectores | El caballo de batalla: integrar con casi cualquier sistema |
| **Conector (prebuilt)** | Acción directa de un conector (SQL, Dataverse, HTTP…) | Operaciones simples y directas |
| **Conector personalizado** | Tú defines una API REST (Fascículo 07) | API propia del cliente |
| **Prompt / AI Builder** | Una llamada a un modelo para una tarea (resumir, clasificar) | Tareas de IA puntuales |
| **REST / HTTP** | Llamada HTTP directa | APIs sin conector |
| **Otro agente / MCP** | Llamar a otro agente o herramienta vía MCP | Arquitecturas multi-agente |

### Cómo fluyen los datos

```
   Usuario: "¿dónde está AUR-100245?"
        │
        ▼
   Tema "Seguimiento de pedido"
        │  (input)  NumeroPedido = "AUR-100245"
        ▼
   ACCIÓN → Flujo Power Automate
        │  consulta el sistema (API/BD)
        ▼
   (output) Estado="En reparto", Fecha="hoy", Transportista="AuroraExpress"
        │
        ▼
   El agente compone el mensaje con esos datos
```

La clave: el flujo recibe **parámetros de entrada** (lo que el agente sabe) y devuelve **parámetros de salida** (lo que el agente dirá). El agente trata el flujo como una **caja negra con contrato**.

---

## 🔧 Práctica paso a paso

> Vamos a simular un "sistema de pedidos" para no depender de infraestructura. Lo haremos con un flujo que devuelve datos simulados según el número. En el Fascículo 07 lo sustituiremos por un **API/Dataverse real** sin tocar el tema (gracias al contrato de entrada/salida).

### Paso 1 · Crear el flujo desde Copilot Studio

1. En AuroraBot, ve a **Actions / Tools → + Add an action → Create a new flow** (o **Power Automate**).
2. Se abre el editor de Power Automate con un disparador especial: **"Run a flow from Copilot"** (a veces *"When Copilot Studio calls a flow"*).

### Paso 2 · Definir el input

1. En el disparador, **+ Add an input** → tipo **Text** → nómbralo `NumeroPedido`.

### Paso 3 · Lógica del flujo (simulada)

Para simular, usa un control **Switch / Condición** sobre `NumeroPedido`. Más simple: usa una acción **Compose** y un **Condition**:

Versión sencilla con variables:

1. Inicializa variables `Estado`, `FechaEntrega`, `Transportista` (Initialize variable, tipo String).
2. Añade un **Condition**:
   - Si `NumeroPedido` **termina en** número par (o simplemente *contiene* `100`) →
     - `Estado = "En reparto"`, `FechaEntrega = "hoy antes de las 20:00"`, `Transportista = "AuroraExpress"`.
   - Si no →
     - `Estado = "Enviado"`, `FechaEntrega = "en 2-3 días laborables"`, `Transportista = "SEUR"`.

> 💡 En un flujo real, aquí pondrías un **HTTP / conector SQL / Dataverse** que consulta el pedido. Lo hacemos en el Fascículo 07.

### Paso 4 · Definir los outputs

1. Añade la acción final del disparador de Copilot: **"Respond to Copilot"** (*Return value(s) to Copilot*).
2. Añade outputs (tipo Text):
   - `Estado` → valor: variable `Estado`
   - `FechaEntrega` → variable `FechaEntrega`
   - `Transportista` → variable `Transportista`
3. **Guarda** el flujo con un nombre claro: `AuroraBot - Consultar estado pedido`.

### Paso 5 · Conectar el flujo en el tema

1. Vuelve a AuroraBot → tema **Seguimiento de pedido**.
2. **Elimina/aparca** la simulación con condiciones del Fascículo 03 (la que usaba "contiene 100"). Vamos a sustituirla por la acción real.
3. Tras capturar `NumeroPedido`, añade un nodo **Call an action** → elige tu flujo `AuroraBot - Consultar estado pedido`.
4. **Mapea el input:** `NumeroPedido` (del tema) → input `NumeroPedido` del flujo.
5. El flujo devuelve `Estado`, `FechaEntrega`, `Transportista` → se guardan en variables del tema.

### Paso 6 · Componer la respuesta

Tras la acción, añade un **Mensaje**:

```
📦 Tu pedido ${NumeroPedido} está: ${Estado}.
Entrega estimada: ${FechaEntrega}.
Transportista: ${Transportista}.
¿Necesitas algo más?
```

### Paso 7 · Manejo de errores

Importante en producción:

1. Tras la acción, comprueba con una **Condición** si `Estado` viene vacío (pedido no encontrado / error).
2. Si vacío → Mensaje: `No he podido localizar ese pedido ahora mismo.` + ofrecer escalar/reintentar.

---

## 🧪 Prueba

1. `¿dónde está mi pedido AUR-100245?` → el agente llama al flujo y responde "En reparto / hoy / AuroraExpress".
2. `AUR-200111` → "Enviado / 2-3 días / SEUR".
3. Provoca un error (desactiva el flujo o pon un número raro) → debe mostrar el mensaje de error, **no romperse**.

- [ ] La acción se ejecuta y los outputs llegan al mensaje.
- [ ] El manejo de error funciona.

---

## 💡 Más allá de la doc oficial

- **El "contrato" entrada/salida es tu mejor amigo.** Si defines bien inputs/outputs, puedes cambiar el interior del flujo (de simulado a API real) **sin tocar el tema**. Diseña pensando en ese contrato desde el principio.
- **Conectores premium = coste y permisos.** HTTP, SQL Server, conectores personalizados y muchos otros son **premium**: requieren licencia adecuada y cuentan para la gobernanza/DLP. Avísalo en la propuesta al cliente.
- **Conexiones y autenticación del flujo.** El flujo se ejecuta con una **conexión** (credenciales). Decide si corre con una **cuenta de servicio** (recomendado en prod) o con la del usuario. Mal configurado = el agente falla solo para algunos usuarios.
- **Latencia.** Cada acción añade segundos. Si encadenas 3 APIs, la conversación se vuelve lenta. Cachea lo que puedas, paraleliza en el flujo y avisa al usuario ("Un momento, consulto tu pedido…").
- **Idempotencia en acciones que escriben.** Crear devolución, abrir ticket: cuidado con dobles envíos si el usuario repite. Devuelve un ID y confírmalo.
- **Errores: nunca exponer el error técnico al cliente.** Captura excepciones en el flujo y devuelve un código/mensaje controlado. El cliente ve "no he podido", no un stack trace.
- **Prompts de AI Builder como acción.** Puedes crear una acción tipo *prompt* que, por ejemplo, **clasifique el sentimiento** del mensaje del cliente y decida si escalar automáticamente. Muy potente combinado con flujos.
- **Power Automate vs. conector directo.** Para algo simple (una llamada HTTP), a veces es más limpio un **conector/HTTP directo** como acción que un flujo entero. Menos piezas, menos latencia.

---

## 📎 Referencias oficiales

- Añadir acciones a un agente: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/advanced-plugin-actions>
- Usar flujos de Power Automate: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/advanced-flow>
- Crear flujos para Copilot: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/flows-overview>
- AI Builder prompts: <https://learn.microsoft.com/en-us/ai-builder/prompts-overview>

---

## ✅ Checklist de la sesión

- [ ] He creado un **flujo de Power Automate** con input y outputs.
- [ ] El tema **Seguimiento de pedido** llama al flujo y usa sus datos.
- [ ] He añadido **manejo de errores**.
- [ ] Entiendo el "contrato" que permite cambiar el backend sin tocar el tema.

➡️ **Siguiente:** [Fascículo 07 · Integración con datos reales: API, Dataverse y Fabric](./07-Integracion-con-Datos-Reales-API-Dataverse-Fabric.md)
