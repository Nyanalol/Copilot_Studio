# Fascículo 09 · Analítica, pruebas y gobernanza

> ⏱️ Duración estimada: 2 h · 🔴 Nivel: avanzado

## 🎯 Objetivos

- Medir el agente con **analítica** (resolución, abandono, temas, CSAT).
- **Probar** sistemáticamente antes de publicar.
- Aplicar **autenticación** de usuario.
- Entender **ALM** (entornos, soluciones) para llevar de Dev → Prod.
- Aplicar **gobernanza y seguridad**: DLP, IA responsable, RGPD.

---

## 📖 Concepto

### Por qué esto separa a un profesional de un aficionado

Hacer un bot que "funciona en la demo" es fácil. Llevarlo a **producción en un cliente** exige: medirlo, probarlo, securizarlo, gobernarlo y poder evolucionarlo sin romper nada. Esta sesión es la que te hace **consultor**, no *hobbyist*.

### Las métricas que importan

| Métrica | Qué te dice | Objetivo en e-commerce |
|---|---|---|
| **Resolution rate** | % conversaciones resueltas por el agente | Maximizar |
| **Escalation rate** | % que acaban en humano | Minimizar (pero sano que exista) |
| **Abandon rate** | % que se van sin resolver | Minimizar |
| **CSAT** | Satisfacción (encuesta) | Maximizar |
| **Temas más usados** | Dónde está la demanda | Priorizar mejoras |
| **Generative answers usage** | Uso/calidad de RAG | Vigilar respuestas sin fuente |

### ALM: Dev → Test → Prod con soluciones

Una **solución (solution)** de Power Platform empaqueta el agente + flujos + entidades + conectores. Se **exporta** de Dev y se **importa** en Test/Prod. Permite:
- No "tocar producción a mano".
- Versionar y volver atrás.
- Automatizar el despliegue (pipelines).

### Gobernanza y seguridad (resumen)

- **Autenticación:** ninguna / Microsoft (Entra ID) / proveedor OAuth propio.
- **DLP (Data Loss Prevention):** políticas que controlan qué conectores pueden combinarse (evitar fugas).
- **IA responsable:** moderación de contenido, límites del modelo, transparencia ("hablas con un bot").
- **RGPD:** minimización de datos, retención de transcripts, derecho al olvido, residencia de datos.

---

## 🔧 Práctica paso a paso

### Parte A · Explora la analítica

1. En AuroraBot, ve a **Analytics / Activity**.
2. Revisa: sesiones, resolution rate, escalations, temas más activos, CSAT.
3. Identifica el **tema con peor resolución** → será tu próxima mejora.
4. Revisa las **conversaciones (transcripts)** para ver dónde se atascan los usuarios.

> 💡 Con pocas pruebas tendrás pocos datos. Genera tráfico probando varios casos para ver poblarse el panel.

### Parte B · Prueba sistemática antes de publicar

1. Usa el **panel de pruebas** con una **batería de casos** (escribe una lista y pásala entera). Para Aurora Store, mínimo:
   - Seguimiento OK / pedido inexistente / formato inválido.
   - Devolución por defecto / por cambio de opinión.
   - FAQ envíos / devoluciones / garantía / fuera de alcance.
   - Recomendación de producto.
   - Escalado a humano.
2. Activa la vista de **"Tracing / variables"** para ver qué tema dispara y cómo viajan las variables.
3. Prueba **frases ambiguas** y errores de escritura (los clientes reales escriben fatal).
4. Verifica que **ningún camino** deja al usuario en un callejón sin salida.

### Parte C · Configura autenticación

1. Ve a **Settings → Security → Authentication**.
2. Opciones:
   - **No authentication:** chat público anónimo (FAQ). Riesgo: no sabes quién es.
   - **Authenticate with Microsoft (Entra ID):** ideal para canal interno (Teams) o clientes con cuenta corporativa.
   - **Custom / OAuth 2.0 genérico:** para integrar el **login propio de Aurora Store** (el cliente ya logado en la web/app).
3. Para Aurora público + área cliente: usa **OAuth del IdP de Aurora**, de modo que cuando el usuario está logado, el agente conoce su identidad y puede consultar **solo sus** pedidos.
4. Tras autenticar, usa `System.User...` / variables del token para personalizar y **evitar pedir el email**.

### Parte D · ALM con soluciones (Dev → Prod)

1. En **Power Apps → Solutions → New solution** (`AuroraBot_Solution`, publisher propio).
2. **Add existing → Chatbot/Agent** → añade AuroraBot (y sus flujos, entidades, conectores).
3. **Export → Managed** (para Prod) / Unmanaged (para seguir desarrollando).
4. En el entorno **Prod**, **Import** la solución.
5. Reconfigura las **conexiones/variables de entorno** (API keys, URLs) en Prod — usa **Environment variables** para no hardcodear secretos.
6. (Pro) Automatiza con **Power Platform Pipelines** o Azure DevOps/GitHub Actions.

### Parte E · DLP e IA responsable

1. (Admin) En **Power Platform Admin Center → Policies → Data policies**, revisa/crea una **política DLP** que clasifique conectores (Business / Non-business / Blocked). Evita que el agente combine conectores que filtren datos.
2. En el agente, revisa **moderación de contenido** (Fascículo 05) y añade en las instrucciones la **transparencia**: el agente debe poder decir que es un asistente virtual.
3. Define **retención de transcripts** y cómo atender solicitudes RGPD (acceso/borrado).

---

## 🧪 Prueba

- [ ] Veo métricas en Analytics y sé cuál es mi peor tema.
- [ ] He pasado una **batería de pruebas** completa sin callejones sin salida.
- [ ] La **autenticación** funciona y personaliza (no pide datos que ya tiene).
- [ ] He exportado el agente como **solución** e importado en otro entorno.

---

## 💡 Más allá de la doc oficial

- **Mide negocio, no solo técnica.** Al cliente no le importa el "resolution rate" abstracto: tradúcelo a **tickets evitados**, **coste por contacto** y **horas de soporte ahorradas**. Esa es la conversación de ROI.
- **Environment variables para secretos/URLs.** Nunca hardcodees la URL del API o la key en el flujo. Usa **environment variables**: al pasar de Dev a Prod solo cambias el valor, no el flujo. Para secretos, intégralas con **Azure Key Vault**.
- **Managed solutions en Prod.** En producción importa siempre **managed** (no se edita a mano → menos accidentes). Edita en Dev, promociona por la pipeline.
- **Pruebas automatizadas.** Copilot Studio tiene capacidades de **testing** y se pueden montar pruebas de regresión. En cliente serio, automatiza una suite que valide los flujos críticos tras cada cambio.
- **Monitoriza con App Insights.** Conecta **Application Insights** para telemetría detallada (errores de acciones, latencias, eventos). Imprescindible para diagnosticar en producción.
- **Plan de contenido vivo.** La política de devoluciones cambia, salen productos nuevos. Define **quién** y **cada cuánto** actualiza conocimiento y prueba. Un agente sin mantenimiento envejece mal.
- **Roles y acceso al agente.** Controla **quién puede editar** el agente (co-autoría) y separa autores de administradores. En cliente, esto es parte del *operating model*.
- **Sesgos y casos límite.** Prueba mensajes problemáticos (quejas duras, intentos de jailbreak, datos sensibles). El agente debe mantener el tono, no filtrar datos y escalar cuando toca.

---

## 📎 Referencias oficiales

- Analítica del agente: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/analytics-overview>
- Autenticación de usuario: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/configuration-end-user-authentication>
- ALM y soluciones: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-export-import-bot>
- Environment variables: <https://learn.microsoft.com/en-us/power-apps/maker/data-platform/environmentvariables>
- DLP / gobernanza: <https://learn.microsoft.com/en-us/power-platform/admin/wp-data-loss-prevention>
- Seguridad y gobernanza de Copilot Studio: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/security-and-governance>
- Application Insights: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/advanced-bot-framework-composer-capture-telemetry>

---

## ✅ Checklist de la sesión

- [ ] Sé leer la analítica y traducirla a ROI.
- [ ] Tengo una **batería de pruebas** y la ejecuto antes de publicar.
- [ ] He configurado **autenticación** y personalización.
- [ ] Manejo **soluciones, environment variables y DLP** para un despliegue gobernado.

➡️ **Siguiente:** [Fascículo 10 · Arquitectura de implementación real](./10-Arquitectura-de-Implementacion-Real.md)
