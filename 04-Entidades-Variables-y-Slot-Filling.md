# Fascículo 04 · Entidades, variables y slot filling

> ⏱️ Duración estimada: 1,5 h · 🟡 Nivel: intermedio

## 🎯 Objetivos

- Dominar **entidades** prebuilt (predefinidas) y **custom** (personalizadas).
- Entender **variables**: ámbitos (tema, global, sistema) y tipos.
- Aplicar **slot filling** (rellenar varios datos de una vez).
- Construir el tema **"Devoluciones"** validando datos del cliente.

---

## 📖 Concepto

### Entidades: cómo el agente "entiende" datos

Una **entidad** es un tipo de información que el agente sabe reconocer dentro de lo que escribe el usuario. Si el cliente dice *"quiero devolver mi pedido AUR-100245 comprado en Madrid"*, el agente puede extraer:

- número de pedido → `AUR-100245`
- ciudad → `Madrid`

Tipos:

1. **Entidades predefinidas (prebuilt):** vienen de serie. Ejemplos: *Email, Number, Date and time, Money, City, Country/Region, Phone number, Age, Boolean, Person name, URL, Color…*
2. **Entidades personalizadas (custom):**
   - **De lista cerrada (closed list):** un conjunto fijo de valores con **sinónimos**. Ej.: motivos de devolución (`Defectuoso`, `No es lo que esperaba`, `Llegó tarde`, `Cambié de opinión`).
   - **Por patrón / expresión regular (regex):** valida formatos. Ej.: número de pedido `AUR-` seguido de 6 dígitos.

### Variables: dónde se guardan los datos

| Ámbito | Prefijo | Vive durante | Uso típico |
|---|---|---|---|
| **De tema** (topic) | (ninguno) | el tema actual | datos temporales |
| **Global** | `Global.` | toda la conversación | email/cliente identificado |
| **Sistema** | `System.` | provisto por la plataforma | canal, idioma, actividad |

Tipos de variable: *String, Boolean, Number, Date time, Table (lista), Record (objeto)…* Las tablas y records son clave cuando una acción devuelve datos estructurados (Fascículo 06–07).

### Slot filling: el "relleno inteligente"

**Slot filling** = el agente rellena varias variables a la vez si el usuario ya dio la info. Si configuras una pregunta para capturar "ciudad" pero el usuario ya la dijo en una frase anterior, el agente **no la vuelve a preguntar**: la "rellena" automáticamente. Esto hace la conversación natural en vez de un interrogatorio.

---

## 🔧 Práctica paso a paso

### Parte A · Crea una entidad personalizada para el número de pedido

1. En AuroraBot, ve a **Settings → Entities** (o **Entities** en el menú del agente).
2. **+ New entity / Nueva entidad** → **Regular expression (regex)** (si tu UI lo ofrece) o **Custom**.
3. Nombre: `NumeroPedidoAurora`.
4. Patrón regex: `AUR-\d{6}`
   - `AUR-` literal, `\d{6}` = exactamente 6 dígitos.
5. Guarda.

> Si tu versión no ofrece regex como tipo directo, crea una **entidad de lista** no sirve aquí; usa la validación dentro del nodo de pregunta o una condición con `Match` de regex. La idea conceptual es la misma: **validar el formato**.

### Parte B · Crea una entidad de lista para motivos de devolución

1. **+ New entity** → **Closed list / Lista**.
2. Nombre: `MotivoDevolucion`.
3. Añade ítems con **sinónimos**:
   - `Producto defectuoso` → sinónimos: *roto, no funciona, defectuoso, averiado*
   - `No es lo que esperaba` → *diferente, no me gusta, no era esto*
   - `Llegó tarde` → *tarde, retraso, fuera de plazo*
   - `Cambié de opinión` → *me arrepentí, ya no lo quiero*
   - `Talla/modelo incorrecto` → *equivocado, no es el modelo, otra talla*
4. Guarda. Activa **Smart matching** si está disponible (tolera errores de escritura).

### Parte C · Mejora "Seguimiento de pedido" con la entidad

1. Abre el tema **Seguimiento de pedido** (Fascículo 03).
2. En el nodo de **Pregunta** del número, cambia **Identify** de "User's entire response" a la entidad **`NumeroPedidoAurora`**.
3. Configura la **reformulación (reprompt):** si no detecta un número válido, que reintente:
   `Ese número no parece válido. El formato es AUR seguido de 6 dígitos, por ejemplo AUR-100245.`
4. Guarda.

### Parte D · Construye el tema "Devoluciones"

1. **Topics → + Add a topic → From blank.** Nombre: `Devoluciones`.
2. **Trigger phrases:**
   ```
   quiero devolver un producto
   cómo hago una devolución
   devolver pedido
   quiero un reembolso
   el producto está defectuoso
   me ha llegado roto
   cambiar un artículo
   ```
3. **Identifica al cliente (global):** Pregunta → `¿Me facilitas el email de tu cuenta de Aurora Store?` → Identify: **Email** (prebuilt) → guarda en `Global.EmailCliente`.
4. **Número de pedido:** Pregunta → `¿Cuál es el número de pedido que quieres devolver?` → Identify: `NumeroPedidoAurora` → variable `NumeroPedido`.
5. **Motivo:** Pregunta → `¿Cuál es el motivo de la devolución?` → Identify: `MotivoDevolucion` (mejor con **opciones/botones** para mostrar la lista).
6. **Ramas por motivo (condición sobre `MotivoDevolucion`):**
   - `Producto defectuoso` → Mensaje: `Lamento el problema 😔. Al ser un defecto, la devolución es gratuita y gestionamos recogida a domicilio.`
   - `Cambié de opinión` → Mensaje: `Sin problema. Recuerda que tienes 30 días y el producto debe estar sin usar y con su embalaje. El coste de envío de la devolución corre a tu cargo (3,99 €).`
   - Else (resto) → Mensaje genérico de proceso estándar.
7. **Confirmación / resumen:** Mensaje:
   `Resumen de tu devolución:
   • Pedido: ${NumeroPedido}
   • Email: ${Global.EmailCliente}
   • Motivo: ${MotivoDevolucion}
   Te enviaremos las instrucciones por email. ¿Confirmas?` (Sí/No)
8. Si **Sí** → Mensaje de confirmación + (en el Fascículo 06 aquí llamaremos a una acción que cree la devolución real). Si **No** → escalar o cerrar.

### Parte E · Observa el slot filling

En las pruebas, escribe directamente:
`Quiero devolver el pedido AUR-100245 porque llegó roto`

Observa: el agente debería **saltarse** la pregunta del número (ya lo diste) y quizá inferir el motivo (`roto` → *Producto defectuoso*). Eso es slot filling actuando.

---

## 🧪 Prueba

1. `quiero devolver un producto` → pide email, pedido y motivo en orden.
2. `Quiero devolver AUR-100245 porque está defectuoso` → debe rellenar pedido y motivo sin volver a preguntarlos (slot filling).
3. Introduce un email mal escrito (`pepe@`) → debe reformular.
4. Introduce `AUR-12` (formato inválido) → debe reformular.

- [ ] Las validaciones de entidad funcionan.
- [ ] El slot filling evita preguntas redundantes.

---

## 💡 Más allá de la doc oficial

- **Las entidades de lista con sinónimos son oro en e-commerce.** Los clientes describen problemas de mil formas ("petó", "no enciende", "vino partido"). Cuantos más sinónimos, menos fallback.
- **Regex de número de pedido = menos llamadas inútiles a tu API.** Validar el formato *antes* de llamar al sistema de pedidos ahorra errores y consumo. Valida en el agente, no solo en el backend.
- **Cuidado con pedir datos personales sin necesidad.** RGPD: pide el mínimo. Si vas a autenticar al usuario (Fascículo 09), quizá ya tengas su email del login y no necesites preguntarlo.
- **Variables Record/Table.** Cuando en el Fascículo 06 una acción devuelva el pedido completo (estado, fecha, líneas), lo recibirás en una variable **Record**. Accederás a sus campos con `Var.Pedido.Estado`, etc. Familiarízate con el concepto ya.
- **Slot filling tiene límites.** Funciona bien con entidades claras; con texto ambiguo puede equivocarse. Diseña las preguntas para **confirmar** datos críticos (importe del reembolso, dirección) antes de ejecutar acciones irreversibles.

---

## 📎 Referencias oficiales

- Entidades y slot filling: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/advanced-entities-slot-filling>
- Usar variables: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-variables>
- Variables de sistema: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-variables-bot>

---

## ✅ Checklist de la sesión

- [ ] He creado entidades **custom** (regex de pedido y lista de motivos).
- [ ] Entiendo ámbitos de variable (tema, `Global.`, `System.`).
- [ ] El tema **Devoluciones** captura y valida email, pedido y motivo.
- [ ] He visto el **slot filling** en acción.

➡️ **Siguiente:** [Fascículo 05 · IA generativa y fuentes de conocimiento](./05-IA-Generativa-y-Fuentes-de-Conocimiento.md)
