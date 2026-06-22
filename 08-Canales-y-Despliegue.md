# Fascículo 08 · Canales y despliegue

> ⏱️ Duración estimada: 2 h · 🔴 Nivel: avanzado

## 🎯 Objetivos

- Entender qué es **publicar** y qué son los **canales**.
- Desplegar AuroraBot en la **web** (widget) y embeberlo en una **app**.
- Conocer **Teams** y **Microsoft 365 Copilot** como canales.
- Usar la **Direct Line API** para integrar el agente en una **app propia** (web/móvil) de forma personalizada.
- Configurar el **handoff** a un agente humano en producción.

---

## 📖 Concepto

### Publicar vs. canales

- **Publicar (publish):** congela el estado actual del agente y lo hace disponible en los canales. Cada cambio que hagas requiere **volver a publicar** para que llegue a los usuarios.
- **Canal (channel):** la superficie donde el usuario habla con el agente.

### Canales disponibles (los principales)

| Canal | Para qué | Esfuerzo |
|---|---|---|
| **Demo website** | Compartir una demo rápida | Mínimo |
| **Custom website (widget)** | Embeber en la web de Aurora con un `<script>`/iframe | Bajo |
| **Microsoft Teams** | Soporte interno / empleados | Bajo |
| **Microsoft 365 Copilot** | Extender el Copilot de M365 con tu agente | Medio |
| **Direct Line (API/SDK)** | Integración a medida en app web/móvil propia | Alto |
| **Telephony / voz** | Canal de voz (IVR) | Alto |
| **Facebook / otros** | Redes/mensajería | Medio |
| **Slack, Twilio (SMS/WhatsApp)** | Vía conectores/Bot Framework | Medio-Alto |

### ¿Qué es Direct Line?

**Direct Line** es una **API REST + SDK** (heredada de Azure Bot Service) que te permite enviar y recibir mensajes con el agente **programáticamente**. Es la vía cuando no quieres el widget estándar, sino **tu propia interfaz de chat** (en tu app React/Angular, app móvil nativa, kiosko, etc.) con tu diseño y control total.

```
Tu app (web/móvil)  ⇄  Direct Line API/SDK  ⇄  Copilot Studio (AuroraBot)
   (tu UI propia)        (token + canal)          (lógica + datos)
```

---

## 🔧 Práctica paso a paso

### Paso 1 · Publicar el agente

1. En AuroraBot, ve a **Publish / Publicar**.
2. Pulsa **Publish**. Espera la confirmación.
3. Cada vez que cambies algo, recuerda **republicar**.

### Paso 2 · Probar en el Demo website

1. Ve a **Channels / Canales → Demo website**.
2. Copia la URL y ábrela: tienes una página de chat funcional para enseñar a stakeholders.

### Paso 3 · Embeber en la web de Aurora Store (widget)

1. **Channels → Custom website** (o **Mobile app / Custom website**).
2. Copia el **snippet de embebido** (un `<script>` o un `<iframe>` con el ID del agente).
3. Pégalo en el HTML de la web de Aurora, por ejemplo antes de `</body>`:

   ```html
   <!-- AuroraBot - widget de Copilot Studio -->
   <iframe
     src="https://web.powerva.microsoft.com/.../webchat?...TU_BOT_ID..."
     style="position:fixed;bottom:20px;right:20px;width:400px;height:600px;border:0;border-radius:12px;box-shadow:0 8px 24px rgba(0,0,0,.2)">
   </iframe>
   ```

   > El snippet exacto te lo da Copilot Studio; no lo escribas a mano. Para producción se suele usar el **Bot Framework Web Chat** (más personalizable) con un token, ver Paso 6.

4. Considera la **seguridad del canal:** por defecto el web chat puede ser abierto. Para un canal público controla el acceso (ver autenticación, Fascículo 09).

### Paso 4 · Publicar en Microsoft Teams (opcional)

1. **Channels → Microsoft Teams → Turn on / Add channel.**
2. Genera el enlace/availability. Útil si Aurora quiere un asistente **interno** para su equipo de soporte (consultar pedidos sin entrar al ERP).

### Paso 5 · Extender Microsoft 365 Copilot (opcional)

1. **Channels → Microsoft 365 Copilot.**
2. Publica el agente como **agente de M365 Copilot**: aparece dentro del Copilot de la organización. Requiere licencias M365 Copilot y aprobación de admin.

### Paso 6 · Integración a medida con Direct Line (app propia)

Para embeber AuroraBot en la app web/móvil de Aurora con UI propia:

1. **Channels → Mobile app / Direct Line** → obtén la información de conexión. Necesitarás un **secret/token** de Direct Line.
2. **Nunca expongas el secret en el cliente.** Monta un **endpoint propio** (Azure Function / tu backend) que:
   - guarda el secret de forma segura,
   - **genera un token temporal** de Direct Line por usuario (`POST https://directline.botframework.com/v3/directline/tokens/generate`).
3. En el front, usa **Bot Framework Web Chat** (web) o el SDK de Direct Line (móvil) con ese token:

   ```html
   <div id="webchat" role="main"></div>
   <script src="https://cdn.botframework.com/botframework-webchat/latest/webchat.js"></script>
   <script>
     // El token lo entrega TU backend, no el secret
     fetch('/api/auroraBot/token')
       .then(r => r.json())
       .then(({ token }) => {
         window.WebChat.renderWebChat(
           {
             directLine: window.WebChat.createDirectLine({ token }),
             styleOptions: { accent: '#6A1B9A', botAvatarInitials: 'AB', userAvatarInitials: 'Tú' }
           },
           document.getElementById('webchat')
         );
       });
   </script>
   ```

4. Para **móvil nativo**, consume la Direct Line REST API (abrir conversación, enviar actividad, recibir vía WebSocket/streaming) desde tu app.

> Direct Line te da control total de la UI (colores, avatares, tarjetas, persistencia) y te permite pasar **contexto** (p. ej. el usuario ya logado en Aurora → pasas su id como variable y el agente no vuelve a pedir el email).

### Paso 7 · Handoff a agente humano (producción)

El Fascículo 03 ofrecía escalado; en producción debe **ir a algún sitio real**:

- **Opción A — Omnichannel / Dynamics 365 Customer Service:** integración nativa; la conversación pasa a una cola de agentes humanos con todo el contexto.
- **Opción B — Tu sistema de tickets/chat** (Zendesk, Salesforce, LiveChat): vía el evento de **hand-off** + un flujo/conector que abre el ticket o transfiere la sesión.
- Configura el **mensaje de transición** y pasa el **transcript** para que el humano no empiece de cero.

---

## 🧪 Prueba

- [ ] He **publicado** el agente.
- [ ] El widget funciona embebido en una página HTML de prueba.
- [ ] (Opcional) He generado un **token de Direct Line** desde un backend y renderizado Web Chat con UI propia.
- [ ] El **handoff** lleva a un destino real (o simulado con un flujo que crea un ticket).

---

## 💡 Más allá de la doc oficial

- **Token, no secret, en el cliente.** El error de seguridad más común: poner el Direct Line **secret** en el JavaScript del navegador. Cualquiera podría usar tu agente. Siempre **token temporal** generado por tu backend.
- **Pasar contexto/identidad al agente.** Con Direct Line puedes enviar una actividad de tipo `event` al inicio con datos del usuario logado (id, nombre, segmento). El agente arranca **sabiendo quién es** → mejor experiencia y menos preguntas. Combínalo con autenticación (Fascículo 09).
- **Republicar es obligatorio.** Mucha gente "cambia algo y no se ve en la web": faltó **Publish**. Inclúyelo en tu checklist de despliegue.
- **Versionado de canal.** El widget apunta a la versión publicada. Ten un entorno de **Test** con su propio widget para validar antes de tocar producción.
- **Rendimiento del Web Chat.** Carga el script de forma diferida, muestra el botón flotante y abre el chat bajo demanda para no penalizar el *Core Web Vitals* de la web de Aurora.
- **Accesibilidad y RGPD en el widget.** Avisa del tratamiento de datos, ofrece el aviso de privacidad y cuida la accesibilidad (lectores de pantalla). Es requisito en un e-commerce europeo.
- **Voz / WhatsApp.** Para canales como WhatsApp o telefonía se suele usar **Azure Bot Service / Communication Services** o partners. Es un proyecto en sí; valóralo aparte.

---

## 📎 Referencias oficiales

- Publicar el agente: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/publication-fundamentals-publish-channels>
- Añadir a sitio web / app móvil: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/publication-connect-bot-to-web-channels>
- Canal Direct Line: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/publication-connect-bot-to-custom-application>
- Bot Framework Web Chat: <https://github.com/microsoft/BotFramework-WebChat>
- Teams: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/publication-add-bot-to-microsoft-teams>
- Hand-off a Omnichannel: <https://learn.microsoft.com/en-us/microsoft-copilot-studio/configuration-hand-off-omnichannel>

---

## ✅ Checklist de la sesión

- [ ] Entiendo publicar vs. canales y los principales canales.
- [ ] He embebido el agente en una web.
- [ ] Sé cómo integrarlo en una **app propia** con Direct Line (token, no secret).
- [ ] Tengo claro cómo configurar el **handoff** real.

➡️ **Siguiente:** [Fascículo 09 · Analítica, pruebas y gobernanza](./09-Analitica-Pruebas-y-Gobernanza.md)
