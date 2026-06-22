# Fascículo 00 · Introducción y caso de estudio

> ⏱️ Duración estimada: 45 min · 🟢 Nivel: introductorio

## 🎯 Objetivos

- Entender **qué es Copilot Studio** y cómo encaja en el ecosistema Microsoft.
- Conocer la diferencia entre **Power Virtual Agents** (nombre antiguo) y **Copilot Studio** (nombre actual).
- Comprender el **caso de estudio Aurora Store** que construirás durante todo el curso.
- Tener una **visión global** de adónde quieres llegar.

---

## 📖 Concepto

### ¿Qué es Copilot Studio?

**Microsoft Copilot Studio** es una herramienta *low-code* / *no-code* para crear **agentes conversacionales** (chatbots y, ahora, agentes con IA generativa) sin necesidad de ser desarrollador. Forma parte de **Microsoft Power Platform**.

> 🔁 **Nota de nomenclatura importante.** El módulo oficial en el que se basa este curso se llama "Power Virtual Agents bots". En **noviembre de 2023**, Microsoft renombró *Power Virtual Agents* a **Copilot Studio** y lo reorientó hacia la **IA generativa**. La terminología cambió:
>
> | Antes (Power Virtual Agents) | Ahora (Copilot Studio) |
> |---|---|
> | Bot | **Agente** (agent / copilot) |
> | Topic (tema) | Topic (sigue igual) |
> | Power Automate flow | Action / Flow |
> | Knowledge base manual | **Generative answers + Knowledge sources** |
>
> En este curso usamos la terminología **actual** (Copilot Studio), pero te indico el término antiguo cuando aparezca en la doc oficial.

### ¿Para qué sirve? (los dos "modos" del producto)

Copilot Studio combina dos paradigmas:

1. **Modo clásico (autoría / flujos)** → tú diseñas la conversación con **temas** (topics): una pregunta dispara un flujo de pasos predecibles. Ideal para procesos guiados (devolver un producto, abrir una incidencia). Es determinista y controlado.

2. **Modo generativo (IA)** → el agente usa un **modelo de lenguaje grande (LLM)** para:
   - **Responder con lenguaje natural** a partir de tus **fuentes de conocimiento** (web, documentos, SharePoint, Dataverse…). Se llama *generative answers*.
   - **Orquestar** por sí mismo qué tema/acción ejecutar según lo que pide el usuario (*generative orchestration*), incluso encadenando varias acciones.

El gran cambio de los últimos años es que **ya no construyes solo "árboles de decisión"**: construyes un agente que entiende e improvisa, y tú le pones límites y herramientas. Aprenderás ambos modos porque en un proyecto real se **combinan**.

### ¿Dónde encaja en Microsoft?

```
┌──────────────────────────────────────────────────────────┐
│                   Microsoft Power Platform                │
│  ┌──────────┐ ┌────────────┐ ┌───────────┐ ┌───────────┐ │
│  │ Power    │ │ Power      │ │ Power     │ │  Copilot  │ │
│  │ Apps     │ │ Automate   │ │ BI        │ │  Studio   │ │
│  └──────────┘ └────────────┘ └───────────┘ └─────┬─────┘ │
│         Datos compartidos en  ► Dataverse ◄        │       │
└────────────────────────────────────────────────────┼───────┘
                                                      │
            Se conecta a ▼ (vía conectores / API / Azure)
   ┌──────────┬─────────────┬──────────────┬──────────────────┐
   │ Microsoft│  Sistemas   │  APIs REST   │ Microsoft Fabric │
   │ 365/Teams│  del cliente│  externas    │  (datos/analytics)│
   └──────────┴─────────────┴──────────────┴──────────────────┘
```

- **Power Automate** → para ejecutar acciones (llamar a APIs, escribir en sistemas).
- **Dataverse** → la base de datos de Power Platform donde puede vivir tu catálogo/pedidos.
- **Microsoft 365 / Teams** → canales donde publicar el agente.
- **Microsoft Fabric** → plataforma de datos/analítica; veremos cómo darle al agente acceso a datos de Fabric (Fascículo 07).

---

## 🛒 El caso de estudio: Aurora Store

Vas a construir **AuroraBot**, el asistente de atención al cliente de un e-commerce de electrónica ficticio llamado **Aurora Store**.

### Perfil del negocio

- **Aurora Store** vende electrónica online (móviles, portátiles, accesorios).
- Tiene **~5.000 SKUs**, **picos de soporte** en campañas (Black Friday, Navidad).
- El equipo de atención al cliente está **saturado** con preguntas repetitivas.

### Lo que AuroraBot tiene que resolver (nuestro backlog del curso)

| Caso de uso | Fascículo donde lo construimos | Tipo |
|---|---|---|
| 📦 Seguimiento de pedido | 03, 06 | Flujo + acción a sistema |
| 🔄 Devoluciones y reembolsos | 04 | Flujo con datos del usuario |
| ❓ FAQ y políticas (envíos, garantías) | 05 | IA generativa sobre conocimiento |
| 🛍️ Recomendación de producto | 05, 07 | IA generativa + datos catálogo |
| 🙋 Escalado a humano | 03, 08 | Handoff a agente en vivo |
| 📊 Medir y mejorar | 09 | Analítica |
| 🌐 Publicar en la web de Aurora y en su app | 08, 10 | Despliegue real |

### El "norte" del curso

Al terminar tendrás:

- Un agente que **conversa con naturalidad** y resuelve casos reales.
- Conectado a **datos reales** (un API de pedidos / Dataverse / Fabric).
- **Desplegado** en una web y embebido en una app.
- Con **analítica, seguridad y gobernanza** de nivel cliente.
- Y entenderás **cómo lo montarías para un cliente de verdad** (arquitectura, costes, opciones).

---

## 💡 Más allá de la doc oficial

- **El módulo oficial de Learn está algo desactualizado** respecto a la UI actual (usa el nombre "Power Virtual Agents"). No te preocupes: los conceptos (temas, entidades, variables, publicación) siguen siendo el núcleo. Este curso te da el "puente" a la versión actual.
- **Copilot Studio ≠ Microsoft 365 Copilot.** Son cosas distintas que comparten marca:
  - *Microsoft 365 Copilot* = el asistente de IA dentro de Word, Teams, Outlook… (producto de usuario final).
  - *Copilot Studio* = la herramienta para **crear** tus propios agentes (lo que estudiamos). Además puedes **extender** M365 Copilot con agentes creados en Copilot Studio.
- **No necesitas programar** para el 80% del curso, pero los fascículos 06–10 te dan la parte "pro" (REST, JSON, arquitectura) que diferencia a alguien que "sabe hacer un bot de demo" de alguien que **lo lleva a producción en un cliente**.

---

## 📎 Referencias oficiales

- Módulo base del curso: <https://learn.microsoft.com/en-us/training/modules/power-virtual-agents-bots/>
- Documentación de Copilot Studio: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/>
- Qué es Copilot Studio (overview): <https://learn.microsoft.com/en-us/microsoft-copilot-studio/fundamentals-what-is-copilot-studio>
- Power Platform overview: <https://learn.microsoft.com/en-us/power-platform/>

---

## ✅ Checklist de la sesión

- [ ] Entiendo que Copilot Studio = la evolución de Power Virtual Agents.
- [ ] Distingo el modo **clásico (temas/flujos)** del modo **generativo (IA)**.
- [ ] Sé qué es Aurora Store y los 7 casos de uso que construiré.
- [ ] Tengo claro el "norte": agente conectado, desplegado y gobernado.

➡️ **Siguiente:** [Fascículo 01 · Fundamentos y entorno](./01-Fundamentos-y-Entorno.md)
