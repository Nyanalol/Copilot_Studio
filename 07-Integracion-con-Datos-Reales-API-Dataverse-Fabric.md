# Fascículo 07 · Integración con datos reales: API, Dataverse y Fabric

> ⏱️ Duración estimada: 2,5 h · 🔴 Nivel: avanzado

## 🎯 Objetivos

- Conectar el agente a **datos reales** del cliente con distintas estrategias.
- Crear un **conector personalizado** para una API REST de pedidos.
- Usar **Dataverse** como base de datos del catálogo/pedidos.
- Integrar **Microsoft Fabric** (data agents / lakehouse) como fuente de datos e inteligencia.
- Saber **elegir** entre estas opciones según el caso del cliente.

---

## 📖 Concepto

### El problema real: ¿dónde están los datos del cliente?

En Aurora Store, los pedidos/stock viven en algún sitio: un ERP, una base de datos SQL, un API propio, un CRM, un data warehouse… Tienes varias vías para que el agente los consulte:

| Estrategia | Cuándo encaja | Complejidad |
|---|---|---|
| **Conector estándar** (SQL, Dataverse, Salesforce…) | El dato está en un sistema con conector ya hecho | Baja |
| **Conector personalizado** (tu API REST) | El cliente tiene una API propia | Media |
| **HTTP request** directo en flujo | Llamada puntual a un endpoint | Baja-Media |
| **Dataverse** | Quieres una BD dentro de Power Platform | Media |
| **Microsoft Fabric** | El dato/analítica vive en el lakehouse/warehouse de Fabric | Media-Alta |

### Microsoft Fabric en una frase

**Microsoft Fabric** es la plataforma unificada de datos y analítica de Microsoft (lakehouse, warehouse, Data Factory, Power BI, tiempo real…). Para un agente, Fabric aporta:
- Un **lugar único** donde ya está consolidado el dato de negocio (ventas, stock, clientes).
- **Data agents de Fabric** (antes "AI Skills"): agentes de IA sobre tus datos de Fabric que entienden preguntas en lenguaje natural y **generan consultas** sobre el lakehouse/warehouse. **Se pueden consumir desde Copilot Studio** como herramienta de conocimiento/acción.

Esto es muy potente para Aurora Store: en lugar de programar cada consulta, un **Fabric data agent** sobre el modelo de ventas puede responder "¿cuántas unidades del modelo X quedan?" o "¿cuál es el plazo medio de entrega esta semana?".

---

## 🔧 Práctica paso a paso

> Elige **una** de las tres rutas según lo que tengas a mano. Las tres terminan dándole "datos reales" al agente. La A (API) es la más universal; la B (Dataverse) la más integrada; la C (Fabric) la más analítica.

### Ruta A · Conector personalizado para tu API REST de pedidos

Supongamos que Aurora Store expone `GET https://api.aurorastore.example/orders/{id}` que devuelve:

```json
{
  "orderId": "AUR-100245",
  "status": "En reparto",
  "carrier": "AuroraExpress",
  "estimatedDelivery": "2026-06-22T20:00:00",
  "items": [{ "sku": "LAP-XPS13", "name": "Portátil XPS 13", "qty": 1 }]
}
```

1. Ve a **Power Apps / Power Automate → Custom connectors → New custom connector → Create from blank** (también desde Copilot Studio: *Actions → New action → Connector*).
2. **General:** host `api.aurorastore.example`, base URL `/`.
3. **Security:** elige el tipo de auth de la API (API Key, OAuth 2.0, etc.). Para Aurora supongamos **API Key** en cabecera `Ocp-Apim-Subscription-Key` o `Authorization`.
4. **Definition → New action:**
   - Operation ID: `GetOrder`
   - Verb: `GET`, path: `/orders/{id}`
   - Define el **parámetro de ruta** `id`.
   - Importa una **respuesta de ejemplo** (pega el JSON de arriba) para que genere el esquema de salida.
5. **Test** con una API key real.
6. En **Copilot Studio → Actions → Add action**, elige tu conector `GetOrder`. Mapea: input `id = NumeroPedido`; outputs `status`, `carrier`, `estimatedDelivery` → variables del tema.
7. Sustituye la acción simulada del Fascículo 06 por esta. **El tema apenas cambia** gracias al contrato.

> 🔐 **Auth de usuario vs. servicio:** si la API requiere identificar al cliente, combina con la **autenticación del agente** (Fascículo 09) para pasar un token del usuario. Para datos no sensibles, una API key de servicio basta.

### Ruta B · Dataverse como base de datos

Útil si quieres que el catálogo/pedidos vivan dentro de Power Platform (o vas a sincronizarlos ahí).

1. En **Power Apps → Tables → New table**, crea `Pedido` con columnas: `NumeroPedido` (texto, clave), `Estado` (choice), `Transportista` (texto), `FechaEntrega` (datetime), `EmailCliente` (texto).
2. Crea `Producto`: `SKU`, `Nombre`, `Precio`, `Stock`, `Categoria`, `Descripcion`.
3. Carga unos registros de ejemplo.
4. En el flujo (Fascículo 06) o como acción directa, usa el **conector de Dataverse**:
   - Acción **"Get a row by ID"** o **"List rows"** con un filtro `NumeroPedido eq 'AUR-100245'`.
5. Devuelve los campos al agente como outputs.
6. **Bonus knowledge:** puedes añadir la tabla `Producto` como **fuente de conocimiento Dataverse** (Fascículo 05) para que la recomendación generativa consulte el catálogo estructurado.

> Dataverse brilla cuando el agente también **escribe** (crear la devolución como fila `Devolucion`, con seguridad por roles).

### Ruta C · Microsoft Fabric (data agent / lakehouse)

Útil si Aurora Store ya tiene su dato consolidado en Fabric (ventas, stock, logística).

**Opción C1 — Consumir un Fabric data agent:**
1. En **Microsoft Fabric**, crea (o pide a tu equipo de datos) un **Data agent** sobre el lakehouse/warehouse con las tablas de ventas/stock/pedidos. Dale instrucciones y ejemplos de preguntas.
2. **Publícalo** y asegúrate de tener permisos.
3. En **Copilot Studio → Actions/Tools → Add → Microsoft Fabric** (o el conector correspondiente) → selecciona tu **data agent**.
4. Ahora AuroraBot puede delegar preguntas analíticas ("¿queda stock del modelo X?", "plazo medio de entrega") al data agent, que **genera la consulta** sobre Fabric y devuelve la respuesta.

**Opción C2 — Consultar datos de Fabric vía SQL endpoint:**
1. El warehouse/lakehouse de Fabric expone un **SQL endpoint**.
2. En un flujo, usa el **conector SQL Server** apuntando al endpoint de Fabric (con auth Entra ID / service principal).
3. Lanza la consulta y devuelve resultados al agente.

> 🧠 **Cuándo Fabric sí merece la pena:** cuando las preguntas son **analíticas/agregadas** (tendencias, métricas, stock consolidado de múltiples almacenes) y el dato ya está modelado en Fabric. Para un simple "estado de mi pedido", un API/Dataverse es más directo y barato.

---

## 🧪 Prueba

- [ ] (Ruta elegida) El agente devuelve **datos reales**, no simulados.
- [ ] El **contrato** entrada/salida se mantuvo: el tema apenas cambió respecto al Fascículo 06.
- [ ] Errores de la fuente (timeout, no encontrado) se manejan con mensaje controlado.
- [ ] (Si Fabric) Una pregunta analítica devuelve un dato calculado sobre el lakehouse.

---

## 💡 Más allá de la doc oficial

- **Capa de API > acceso directo a BD.** En cliente real, evita que el agente toque la BD de producción directamente. Lo limpio: una **API** (o Dataverse como capa intermedia) que el equipo del cliente controla, versiona y protege. Así el agente nunca tumba la BD ni ve datos de más.
- **Seguridad a nivel de fila.** El agente debe ver **solo** lo que el usuario puede ver. Con Dataverse usa roles de seguridad; con API, pasa el **token del usuario**; con Fabric, respeta el modelo de permisos. Nunca devuelvas el pedido de otro cliente.
- **Fabric data agents: gobernanza del dato.** El data agent responde lo que el modelo de datos le permite. Cura bien qué tablas/medidas expone y documenta los nombres de columnas en lenguaje de negocio (mejora la calidad de las consultas generadas).
- **Coste:** conectores premium (HTTP, SQL, custom) requieren licencia; Fabric consume **capacidad (F SKU)**; Dataverse consume almacenamiento. Suma todo en la estimación (Fascículo 10).
- **Latencia y cache.** Datos que cambian poco (descripción de producto, políticas) → cachéalos o ponlos como conocimiento. Datos volátiles (stock, estado) → consulta en vivo pero con timeouts y *fallbacks*.
- **Idempotencia y trazabilidad.** Toda acción de escritura (crear devolución) debe registrar un ID y quedar auditada. El cliente querrá rastrear qué hizo el agente.
- **MCP (Model Context Protocol).** Copilot Studio admite conectar herramientas vía **MCP**, lo que permite exponer sistemas del cliente como herramientas estandarizadas para el agente. Útil en arquitecturas modernas multi-herramienta.

---

## 📎 Referencias oficiales

- Conectores personalizados: <https://learn.microsoft.com/en-us/connectors/custom-connectors/>
- Conector de Dataverse: <https://learn.microsoft.com/en-us/connectors/commondataserviceforapps/>
- Dataverse como conocimiento: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/knowledge-add-dataverse>
- Microsoft Fabric data agents: <https://learn.microsoft.com/en-us/fabric/data-science/concept-data-agent>
- Consumir Fabric data agent en Copilot Studio: <https://learn.microsoft.com/en-us/fabric/data-science/data-agent-microsoft-copilot-studio>
- MCP en Copilot Studio: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/agent-extend-action-mcp>

---

## ✅ Checklist de la sesión

- [ ] Sé elegir entre **API / Dataverse / Fabric** según el caso.
- [ ] He conectado el agente a una fuente de datos **real** (al menos una ruta).
- [ ] Entiendo seguridad por fila, coste y latencia de cada opción.
- [ ] Conozco los **Fabric data agents** y cuándo aportan valor.

➡️ **Siguiente:** [Fascículo 08 · Canales y despliegue](./08-Canales-y-Despliegue.md)
