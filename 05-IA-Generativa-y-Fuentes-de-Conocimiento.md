# Fascículo 05 · IA generativa y fuentes de conocimiento

> ⏱️ Duración estimada: 2 h · 🟡🔴 Nivel: intermedio-avanzado

## 🎯 Objetivos

- Entender **generative answers** (respuestas generativas) y **knowledge sources**.
- Conectar fuentes de conocimiento (web, documentos, SharePoint, Dataverse).
- Activar y entender la **orquestación generativa** (el agente decide solo).
- Construir la **FAQ inteligente** de Aurora Store y mejorar la recomendación de producto.
- Aplicar **buenas prácticas anti-alucinación** (crítico en e-commerce).

---

## 📖 Concepto

### El salto generativo

En el modo clásico (Fascículos 03–04), tú escribes cada respuesta. No escala: no puedes prever las miles de formas de preguntar "¿hacéis envíos a Canarias?".

Con **generative answers**, el agente:
1. Toma la pregunta del usuario.
2. Busca en tus **fuentes de conocimiento** (knowledge sources).
3. **Redacta** una respuesta en lenguaje natural **basada en esas fuentes**, con citas.

Esto es **RAG** (Retrieval-Augmented Generation): el LLM no "se inventa" la respuesta, la **fundamenta** en tus documentos. Bien configurado, reduce drásticamente las alucinaciones.

### Tipos de fuentes de conocimiento

| Fuente | Ejemplo en Aurora Store |
|---|---|
| **Sitios web públicos** | Páginas de ayuda, condiciones de envío publicadas |
| **Documentos** (PDF, Word, etc.) | Política de devoluciones, garantías, manual |
| **SharePoint / OneDrive** | Documentación interna de procesos |
| **Dataverse** | Tablas de catálogo, FAQ estructurada |
| **Conocimiento empresarial** (Graph, etc.) | Datos de M365 (con permisos) |
| **Conectores / orígenes externos** | Otros sistemas vía conector |

### Orquestación generativa vs. clásica

- **Clásica:** el agente solo dispara temas según **trigger phrases**. Tú controlas todo el flujo.
- **Generativa (orchestration):** el agente usa el LLM para **decidir** qué hacer ante cada mensaje:
  - elegir el **tema** adecuado (usando su **descripción**, no solo frases),
  - llamar a una **acción/herramienta**,
  - responder con **generative answers**,
  - incluso **encadenar** varios pasos para una petición compleja.

La orquestación generativa es lo que convierte un "chatbot de árbol" en un "agente". **La activaremos ahora.**

---

## 🔧 Práctica paso a paso

### Parte A · Prepara el conocimiento de Aurora Store

Crea (o reúne) un par de documentos/páginas que servirán de base. Para practicar, crea un PDF o usa una web. Contenido sugerido **Política de Aurora Store**:

```
POLÍTICA DE ENVÍOS
- Envío estándar gratuito en pedidos > 50 €. Plazo 2-3 días laborables.
- Envío urgente 24h: 6,99 €.
- Envíos a Península, Baleares, Canarias, Ceuta y Melilla.
- Canarias/Ceuta/Melilla: plazo 4-6 días, posibles gastos de aduana.

POLÍTICA DE DEVOLUCIONES
- Plazo de 30 días desde la recepción.
- Producto sin usar y con embalaje original.
- Defectos de fábrica: devolución gratuita con recogida.
- Cambio de opinión: gastos de devolución 3,99 € a cargo del cliente.

GARANTÍAS
- 3 años de garantía legal en toda la electrónica.
- Soporte técnico: soporte@aurorastore.example
```

### Parte B · Añade la fuente de conocimiento

1. En AuroraBot, ve a **Knowledge / Conocimiento**.
2. **+ Add knowledge**.
3. Elige el tipo:
   - **Documentos / Upload files** → sube el PDF de políticas, **o**
   - **Public website** → pega la URL de una página pública de ayuda.
4. Dale un **nombre descriptivo** y, muy importante, una **descripción** clara de qué contiene (la orquestación la usa para decidir cuándo consultarla):
   `Políticas de Aurora Store: envíos, plazos, devoluciones, garantías y soporte.`
5. Guarda y **espera a que se indexe** (puede tardar unos minutos).

### Parte C · Activa las respuestas generativas y la orquestación

1. Ve a **Settings → Generative AI**.
2. Selecciona el modo **Generative (orchestration)** (en vez de Classic).
3. Ajusta el **nivel de moderación de contenido** (content moderation): para e-commerce, un nivel **alto** reduce respuestas arriesgadas.
4. Revisa la opción de **usar conocimiento general del modelo**: para Aurora Store, **desactívala o limítala** para que **solo responda con tus fuentes** (menos alucinaciones). Deja activado solo si quieres respuestas de cultura general.
5. Guarda.

### Parte D · Afina las instrucciones para la FAQ

Vuelve a las **Instructions** del agente (Fascículo 02) y añade:

```
Cuando el cliente pregunte sobre envíos, plazos, devoluciones, garantías o
soporte, responde usando EXCLUSIVAMENTE la información de las fuentes de
conocimiento de Aurora Store. Si la respuesta no está en esas fuentes, dilo
con claridad y ofrece escalar a un agente humano. Nunca inventes plazos,
precios, importes ni condiciones.
```

### Parte E · Mejora la recomendación de producto (tema + generativo)

Crea un tema ligero `Recomendación de producto`:

1. **Trigger phrases:** `recomiéndame un portátil`, `qué móvil me aconsejas`, `busco unos auriculares`, `qué me recomiendas para…`.
2. **Pregunta** los criterios: presupuesto, uso (trabajo/juego/foto), preferencia de marca → guarda en variables.
3. Nodo **Create generative answers** o un mensaje que use el conocimiento del catálogo (en el Fascículo 07 conectaremos el catálogo real desde Dataverse/Fabric; por ahora puede apoyarse en una fuente de conocimiento con una lista de productos destacados).

> En producción, la recomendación seria se hace con **datos en vivo** (stock, precio) vía acción/API, no solo con un documento. Lo construimos en los Fascículos 06–07.

### Parte F · Prueba y observa las citas

1. Pregunta: `¿hacéis envíos a Canarias?` → debe responder con el plazo y la nota de aduanas, **citando** la fuente.
2. `¿cuánto tiempo tengo para devolver algo?` → "30 días…".
3. `¿tenéis garantía?` → "3 años…".
4. Pregunta algo **fuera** del conocimiento: `¿cuál es vuestra facturación anual?` → debe decir que no dispone de esa información y ofrecer escalar (no inventar).

---

## 🧪 Prueba

- [ ] Las respuestas de envíos/devoluciones/garantías salen de tus fuentes y muestran **citas**.
- [ ] Una pregunta fuera de alcance **no se inventa** la respuesta.
- [ ] La orquestación generativa elige bien entre FAQ, seguimiento y devoluciones según lo que pides.

---

## 💡 Más allá de la doc oficial

- **La descripción de cada fuente y de cada tema es el "mando" de la orquestación.** El LLM decide a partir de esas descripciones. Si la orquestación elige mal, casi siempre es porque las descripciones son pobres o solapadas. Inviértele tiempo.
- **Anti-alucinación en e-commerce (lo más importante):**
  - Desactiva el "conocimiento general" si no lo necesitas.
  - Instrucciones explícitas de "no inventar precios/stock/plazos".
  - Datos volátiles (precio, stock, estado de pedido) **nunca** vía documento → siempre vía **acción/API** (Fascículos 06–07). Un PDF con precios queda obsoleto y el agente mentirá con total seguridad.
- **Calidad de las fuentes = calidad de las respuestas.** Documentos bien estructurados (encabezados, listas, frases cortas) se indexan y citan mejor que un PDF escaneado o un muro de texto.
- **Refresco de la indexación.** Si actualizas la política, hay que **reindexar**. Para webs hay un refresco; para documentos, vuelve a subirlos. Planifica el mantenimiento del conocimiento con el cliente.
- **Idioma del conocimiento.** Puedes tener fuentes en varios idiomas; el agente responde en el idioma del usuario. Para Aurora Store, ten las políticas en español.
- **Mide la "groundedness".** En analítica (Fascículo 09) podrás ver tasa de resolución y si las respuestas generativas se están usando. Vigila respuestas sin cita: son señal de riesgo.
- **Prompts/Plugins de IA (AI Builder).** Más allá de RAG, puedes crear **prompts personalizados** (con AI Builder / GPT) para tareas como "resume esta incidencia" o "clasifica el sentimiento del cliente". Útil como acción dentro de un tema (lo mencionamos en el Fascículo 06).

---

## 📎 Referencias oficiales

- Respuestas generativas (overview): <https://learn.microsoft.com/en-us/microsoft-copilot-studio/nlu-boost-conversations>
- Fuentes de conocimiento: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/knowledge-copilot-studio>
- Orquestación generativa: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/advanced-generative-actions>
- Moderación de contenido / IA responsable: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/nlu-generative-answers-content-moderation>

---

## ✅ Checklist de la sesión

- [ ] He añadido **fuentes de conocimiento** y se han indexado.
- [ ] He activado la **orquestación generativa** y ajustado moderación.
- [ ] La FAQ responde citando fuentes y **no alucina** fuera de alcance.
- [ ] Entiendo por qué los datos volátiles van por API, no por documento.

➡️ **Siguiente:** [Fascículo 06 · Acciones y Power Automate](./06-Acciones-y-Power-Automate.md)
