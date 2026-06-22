# Fascículo 10 · Arquitectura de implementación real (en un cliente)

> ⏱️ Duración estimada: 2 h · 🔴 Nivel: avanzado / consultoría · 🏁 Cierre del curso

## 🎯 Objetivos

- Juntar todo: **cómo se monta AuroraBot para un cliente real**.
- Comparar las opciones de **despliegue** (web, app, API/Direct Line, Teams, Fabric).
- Diseñar la **arquitectura de referencia** end-to-end.
- Estimar **costes**, definir el **operating model** y el **roadmap**.
- Tener una **plantilla de propuesta** reutilizable.

---

## 📖 Concepto

### La pregunta del cliente: "¿esto cómo lo implemento? ¿en Fabric, en API, en una app…?"

No es "o lo uno o lo otro": son **capas distintas** que se combinan. Mapa mental:

```
┌────────────────────────────────────────────────────────────────────┐
│ CAPA DE EXPERIENCIA (dónde habla el usuario)                        │
│  Web Aurora (widget)  ·  App móvil (Direct Line)  ·  Teams  ·  M365 │
└───────────────────────────────┬────────────────────────────────────┘
                                 │
┌───────────────────────────────▼────────────────────────────────────┐
│ CAPA DE AGENTE (el cerebro) — COPILOT STUDIO                        │
│  Instrucciones · Temas · Entidades · Orquestación generativa        │
│  Conocimiento (RAG) · Acciones/Tools · Autenticación                │
└───────────────────────────────┬────────────────────────────────────┘
                                 │ (acciones / conectores / MCP)
┌───────────────────────────────▼────────────────────────────────────┐
│ CAPA DE INTEGRACIÓN — POWER AUTOMATE / CONECTORES / API             │
│  Flujos · Conector personalizado · HTTP · Dataverse                 │
└───────────────────────────────┬────────────────────────────────────┘
                                 │
┌───────────────────────────────▼────────────────────────────────────┐
│ CAPA DE DATOS (la verdad del negocio)                               │
│  API/ERP de pedidos · BD SQL · Dataverse · MICROSOFT FABRIC         │
│  (lakehouse/warehouse, data agents, analítica)                      │
└─────────────────────────────────────────────────────────────────────┘
```

- **"En una app"** → capa de experiencia (Direct Line / widget).
- **"En un API"** → capa de integración (conector/HTTP a la API del cliente).
- **"En Fabric"** → capa de datos (consultas analíticas / data agents).

Casi siempre el resultado es **una combinación**: app/web arriba, Copilot Studio en medio, API + Fabric abajo.

---

## 🧩 Opciones de despliegue comparadas

| Escenario del cliente | Capa de experiencia | Integración | Datos | Notas |
|---|---|---|---|---|
| E-commerce con web propia | Widget en la web + Direct Line en área cliente | Conector a API de pedidos | API/ERP + Fabric (analítica) | El caso de Aurora Store |
| Soporte interno de empleados | Teams | Flujos a sistemas internos | Dataverse / SQL | Rápido de montar |
| Extensión del Copilot corporativo | Microsoft 365 Copilot | Acciones M365/Graph | M365 + Dataverse | Requiere licencias M365 Copilot |
| App móvil de marca | Direct Line SDK (UI nativa) | API REST propia | API + Fabric | Máximo control de UX |
| Analítica conversacional | Cualquiera | Fabric data agent | Microsoft Fabric | Preguntas agregadas/insight |

---

## 🏗️ Arquitectura de referencia para Aurora Store

```
            ┌──────────────┐         ┌──────────────────────┐
  Cliente → │ Web Aurora    │  widget │  Bot Framework        │
            │ (e-commerce)  │────────▶│  Web Chat (token)     │
            └──────────────┘          └──────────┬───────────┘
                                                  │ Direct Line
            ┌──────────────┐  SDK / token         ▼
  Cliente → │ App móvil     │──────────▶  ┌─────────────────────────┐
            └──────────────┘             │   COPILOT STUDIO         │
                                         │   AuroraBot              │
            ┌──────────────┐  Entra ID   │  - Orquestación gen.     │
  Empleado→ │ Teams         │───────────▶│  - Temas (pedidos/devol.)│
            └──────────────┘             │  - Conocimiento (RAG)    │
                                         │  - Auth OAuth (login web)│
                                         └───────┬───────┬─────────┘
                          acciones/flujos        │       │  Fabric data agent
                                  ┌───────────────┘       └────────────┐
                                  ▼                                    ▼
                       ┌─────────────────────┐            ┌────────────────────┐
                       │ API REST Pedidos     │            │ Microsoft Fabric    │
                       │ (vía conector custom)│            │ Lakehouse/Warehouse │
                       │  + Azure Function     │            │ (ventas, stock,     │
                       │  (token Direct Line,  │            │  logística)         │
                       │   secrets en KeyVault)│            │  + Data agent       │
                       └──────────┬───────────┘            └────────────────────┘
                                  ▼
                       ┌─────────────────────┐
                       │ ERP / BD de Aurora   │
                       └─────────────────────┘

  Transversal: Entra ID (identidad) · Key Vault (secretos) ·
               App Insights (telemetría) · DLP/Solutions (gobernanza)
```

### Decisiones clave de esta arquitectura

1. **Identidad unificada:** el usuario logado en la web/app pasa su identidad al agente (OAuth) → el agente consulta **solo sus** pedidos.
2. **Backend de tokens (Azure Function):** genera tokens de Direct Line; los **secretos** viven en **Key Vault**, nunca en el front.
3. **API como contrato:** el agente no toca la BD/ERP directamente; habla con una **API** estable y segura.
4. **Fabric para insight:** consultas analíticas y stock consolidado vía **data agent**; lo transaccional (estado de un pedido concreto) vía **API**.
5. **Gobernanza:** soluciones managed, environment variables, DLP, telemetría en App Insights.

---

## 💶 Estimación de costes (cómo plantearla al cliente)

Componentes a sumar:

| Componente | Driver de coste |
|---|---|
| **Copilot Studio** | Mensajes/mes (packs de capacidad). Estimar nº conversaciones × mensajes/conv. |
| **Power Automate / conectores premium** | Si usas HTTP/SQL/custom connector |
| **Dataverse** | Almacenamiento (si guardas pedidos/devoluciones) |
| **Microsoft Fabric** | Capacidad F SKU (si integras Fabric) |
| **Azure** | Function (tokens), Key Vault, App Insights, ancho de banda |
| **M365 Copilot** | Solo si publicas como agente de M365 Copilot |
| **Servicios/implantación** | Tu trabajo: análisis, build, pruebas, despliegue, soporte |

**Método rápido de dimensionamiento de mensajes:**
`conversaciones_mes × mensajes_por_conversación × factor_generativo`
Una FAQ simple consume poco; un flujo con acciones + respuestas generativas consume más. Pide al cliente el **volumen de contactos de soporte** actual y modela 2–3 escenarios (bajo/medio/pico campaña).

> ⚠️ Los precios cambian; consulta **siempre** la página de precios oficial vigente y la calculadora de Azure antes de cerrar números.

---

## 🔁 Operating model (cómo se mantiene vivo)

- **Roles:** Product owner del agente · Autor/maker · Admin de Power Platform · Equipo de datos (API/Fabric) · Soporte (handoff).
- **Ciclo de mejora continua:** analítica → detectar temas flojos → mejorar conocimiento/temas → probar → publicar.
- **Gestión de conocimiento:** responsable y cadencia de actualización de políticas/catálogo.
- **Gobernanza:** DLP, revisión de seguridad, RGPD, auditoría de acciones.
- **ALM:** Dev → Test → Prod con soluciones y pipelines.

---

## 🗺️ Roadmap de implantación sugerido (para un cliente)

| Fase | Semanas | Entregable |
|---|---|---|
| **0. Discovery** | 1–2 | Casos de uso priorizados, datos disponibles, KPIs, arquitectura objetivo |
| **1. MVP** | 2–4 | FAQ generativa + seguimiento de pedido (1 API), widget en web, métricas básicas |
| **2. Ampliación** | 3–6 | Devoluciones, recomendación, auth con login, handoff real |
| **3. Datos avanzados** | 2–4 | Integración Fabric/Dataverse, insight, personalización |
| **4. Hardening** | 2–3 | Seguridad, DLP, ALM, App Insights, pruebas de regresión |
| **5. Go-live + mejora** | continuo | Producción, monitorización, iteración por analítica |

---

## 📋 Plantilla de propuesta (reutilizable)

```
1. Contexto y objetivos (qué dolor resolvemos, KPIs)
2. Casos de uso priorizados (con valor esperado por caso)
3. Arquitectura propuesta (capas: experiencia / agente / integración / datos)
4. Canales de despliegue (web, app, Teams, …)
5. Integraciones (API/Dataverse/Fabric) y dependencias del cliente
6. Seguridad, identidad y cumplimiento (Entra ID, DLP, RGPD)
7. Plan de entrega (fases, hitos, equipo)
8. Modelo de costes (licencias + Azure + servicios) en 3 escenarios
9. Operating model (roles, mantenimiento, mejora continua)
10. Riesgos y mitigaciones
```

---

## 💡 Más allá de la doc oficial

- **Empieza por el MVP de mayor valor/menor fricción.** En e-commerce suele ser **seguimiento de pedido + FAQ de envíos/devoluciones**: alto volumen, datos accesibles, ROI rápido. Demuestra valor antes de pedir integrar Fabric o el ERP entero.
- **El dato manda.** El 80% del esfuerzo real no es el bot, es **acceder a datos limpios y seguros**. Negocia pronto con el equipo de datos del cliente (APIs, permisos, Fabric).
- **No sobre-ingenierizar.** No todo cliente necesita Direct Line + Fabric + multi-agente. A veces un **widget + un API** resuelve el 90%. Ajusta la arquitectura al problema, no al revés.
- **Transaccional vs. analítico:** estado de *un* pedido → API; "¿cómo van las ventas/stock?" → Fabric. No confundas las herramientas.
- **Multi-agente / MCP:** para clientes grandes, AuroraBot puede **orquestar** otros agentes especializados (logística, facturación) vía conexiones/MCP. Es la dirección del producto; tenlo en el radar para el roadmap.
- **Mide y comunica ROI** desde el día 1: tickets desviados, CSAT, coste por contacto. Es lo que renueva el contrato.

---

## 📎 Referencias oficiales

- Arquitectura y extensibilidad de Copilot Studio: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/fundamentals-what-is-copilot-studio>
- Precios de Copilot Studio: <https://www.microsoft.com/en-us/microsoft-copilot/microsoft-copilot-studio>
- Conectar a aplicación personalizada (Direct Line): <https://learn.microsoft.com/en-us/microsoft-copilot-studio/publication-connect-bot-to-custom-application>
- Microsoft Fabric: <https://learn.microsoft.com/en-us/fabric/>
- Guía de adopción de Power Platform (CoE): <https://learn.microsoft.com/en-us/power-platform/guidance/adoption/methodology>

---

## ✅ Checklist de la sesión (y del curso)

- [ ] Sé explicar al cliente "web vs. app vs. API vs. Fabric" como **capas combinables**.
- [ ] Puedo dibujar una **arquitectura de referencia** end-to-end.
- [ ] Sé **estimar costes** y montar una **propuesta**.
- [ ] Tengo un **roadmap** y un **operating model** para implantar y mantener.

---

## 🎓 ¡Has terminado el curso!

Has construido AuroraBot desde cero: temas, entidades, IA generativa, acciones, datos reales (API/Dataverse/Fabric), despliegue (web/app/Teams/Direct Line), analítica, gobernanza y una arquitectura lista para cliente.

**Próximos pasos sugeridos:**
- Repite el agente en tu propio entorno end-to-end sin mirar la guía.
- Sustituye Aurora Store por un caso real (tuyo o de un cliente).
- Saca certificación: **PL-900** (Power Platform Fundamentals) y profundiza con la documentación de Copilot Studio.
- Mantente al día: el producto evoluciona rápido (agentes autónomos, multi-agente, MCP).

⬅️ Volver al [índice del curso](./README.md)
