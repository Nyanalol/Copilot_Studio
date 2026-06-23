# 🎓 Formación Copilot Studio — Curso práctico por fascículos

Curso autodidacta y **práctico** para aprender **Microsoft Copilot Studio** (la evolución de *Power Virtual Agents*) de cero a producción, construyendo un caso real de **e-commerce** sesión a sesión.

> Basado en el módulo oficial de Microsoft Learn
> [Get started with Power Virtual Agents / Copilot Studio](https://learn.microsoft.com/en-us/training/modules/power-virtual-agents-bots/),
> ampliado con todo lo que la documentación oficial no explica bien: integración con APIs reales, Dataverse, Microsoft Fabric, embebido en apps y gobernanza.

---

## 🛒 El caso de estudio: "Aurora Store"

A lo largo del curso construirás **AuroraBot**, el agente de atención al cliente de **Aurora Store**, un e-commerce de electrónica. El agente tendrá que resolver de forma autónoma:

1. **Seguimiento de pedidos** ("¿Dónde está mi pedido?")
2. **Devoluciones y reembolsos** (proceso guiado)
3. **Preguntas de producto / FAQ** (con IA generativa sobre el catálogo y políticas)
4. **Recomendación de productos**
5. **Escalado a un agente humano** cuando haga falta

Cada fascículo añade una capa a este agente. Al final tendrás un agente realista, conectado a datos reales y desplegado en una web/app.

---

## 📚 Índice de fascículos (sesiones)

| # | Sesión | Qué aprenderás | Entregable |
|---|--------|----------------|------------|
| 00 | [Introducción y caso de estudio](./00-Introduccion-y-Caso-de-Estudio.md) | Qué es Copilot Studio, cómo funciona el curso, el escenario Aurora Store | Plan mental del proyecto |
| 01 | [Fundamentos y entorno](./01-Fundamentos-y-Entorno.md) | Conceptos, licencias, Power Platform, entornos, primer acceso | Entorno listo y acceso a Copilot Studio |
| 01b | [Anexo: licencia M365 Copilot y tenant corporativo](./01b-Anexo-Licencia-M365-Copilot-y-Entorno-Corporativo.md) | Qué cubre M365 Copilot vs Copilot Studio, qué entorno elegir en una empresa | Entorno DEV correcto + capacidad |
| 02 | [Crea tu primer agente](./02-Crear-tu-Primer-Agente.md) | Crear AuroraBot, anatomía del agente, probarlo | Agente base creado |
| 03 | [Temas y flujos de conversación](./03-Temas-y-Flujos-de-Conversacion.md) | Topics, trigger phrases, nodos, ramas, "Seguimiento de pedido" | Tema de seguimiento funcionando |
| 04 | [Entidades, variables y slot filling](./04-Entidades-Variables-y-Slot-Filling.md) | Entidades prebuilt/custom, variables, slot filling, "Devoluciones" | Tema de devoluciones con datos |
| 05 | [IA generativa y conocimiento](./05-IA-Generativa-y-Fuentes-de-Conocimiento.md) | Generative answers, knowledge sources, orquestación generativa | FAQ inteligente del catálogo |
| 06 | [Acciones y Power Automate](./06-Acciones-y-Power-Automate.md) | Actions, flujos, conectores, llamar a sistemas externos | Pedido consultado vía flujo |
| 07 | [Integración con datos reales: API, Dataverse y Fabric](./07-Integracion-con-Datos-Reales-API-Dataverse-Fabric.md) | Conectores personalizados, REST, Dataverse, Microsoft Fabric data agents | Agente conectado a datos reales |
| 08 | [Canales y despliegue](./08-Canales-y-Despliegue.md) | Web, Teams, M365 Copilot, embebido en app, Direct Line API | Agente publicado y embebido |
| 09 | [Analítica, pruebas y gobernanza](./09-Analitica-Pruebas-y-Gobernanza.md) | Testing, métricas, ALM, seguridad, DLP, autenticación | Agente monitorizado y gobernado |
| 10 | [Arquitectura de implementación real](./10-Arquitectura-de-Implementacion-Real.md) | Cómo se monta en un cliente real (web, app, API, Fabric), patrones y costes | Diseño de arquitectura completo |

---

## 🧭 Cómo usar este curso

- **Ritmo sugerido:** 1 fascículo por sesión de estudio (1–2 horas cada uno).
- Cada fascículo tiene esta estructura fija:
  - 🎯 **Objetivos**
  - 📖 **Concepto** (la teoría mínima necesaria)
  - 🔧 **Práctica paso a paso** (lo que haces en Aurora Store)
  - 🧪 **Prueba** (cómo validar que funciona)
  - 💡 **Más allá de la doc oficial** (trucos, errores comunes, buenas prácticas)
  - 📎 **Referencias oficiales**
  - ✅ **Checklist de la sesión**
- Los **enlaces oficiales** apuntan a `learn.microsoft.com`. La UI de Copilot Studio cambia a menudo; si algo no coincide exactamente, los conceptos siguen siendo válidos.

## ✅ Requisitos previos

- Una cuenta de Microsoft 365 / Power Platform (vale una **prueba gratuita**, ver Fascículo 01).
- Navegador moderno (Edge o Chrome).
- Ganas de trastear. No necesitas saber programar, aunque en los fascículos 06–10 tocaremos algo de JSON, REST y arquitectura.

## 🗂️ Estado del progreso

Marca aquí tu avance:

- [ ] 00 · Introducción
- [ ] 01 · Fundamentos y entorno
- [ ] 02 · Primer agente
- [ ] 03 · Temas
- [ ] 04 · Entidades y variables
- [ ] 05 · IA generativa
- [ ] 06 · Acciones y Power Automate
- [ ] 07 · Datos reales (API/Dataverse/Fabric)
- [ ] 08 · Canales y despliegue
- [ ] 09 · Analítica y gobernanza
- [ ] 10 · Arquitectura real

---

*Curso creado como material de formación personal. Terminología actualizada a Copilot Studio (2024–2026).*
