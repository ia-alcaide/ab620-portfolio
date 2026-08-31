# ab620-portfolio

# AB-620 · AI Agent Builder Associate — Portfolio

Itinerario personal de preparación para la certificación
Microsoft Certified: AI Agent Builder Associate (Exam AB-620).

## Dominios del examen
- Plan and configure agent solutions (30-35%)
- Integrate and extend agents in Copilot Studio (40-45%)
- Test and manage agents (20-25%)

## Progreso
- [x] Día 1 — Entorno preparado
  - Tenant creado: tudominio.onmicrosoft.com
  - Copilot Studio: trial activo
  - Power Apps Developer Plan: activo

- [x] Agente Creado
  - <img width="711" height="272" alt="imagen" src="https://github.com/user-attachments/assets/5530b824-a727-4a6f-90dc-e49a2b8838a9" />

- [x] Agente alimentado indicando comportamiento y limitando su conocimiento solo a las fuentes proporcionadas
  <img width="739" height="290" alt="imagen" src="https://github.com/user-attachments/assets/1a91be09-8fb4-487e-927c-83e66c072682" />

- [x] Día 2 — Agent flow con confirmación humana (RegistrarSolicitud)
  - Funciona de extremo a extremo: input → aprobación → condición → 
    variable → respuesta al agente
  - Probado con éxito: "Tu solicitud de vacaciones se ha enviado correctamente."
  - Limitación descubierta: depende de aprobar en <100s, no viable para
    producción con usuarios reales que no están pendientes al instante
 <img width="519" height="230" alt="imagen" src="https://github.com/user-attachments/assets/3113de8a-2337-4852-91e1-01415694c4fb" />

 - [x] Día 3 — HITL asíncrono + gestión de errores
  - Rediseño: "Responder al agente" movido justo tras el trigger,
    con mensaje inmediato fijo ("Solicitud recibida...")
  - Aprobación + condición + notificación por email corren en
    segundo plano, sin bloquear la conversación
  - Comparativa documentada: HITL síncrono (Día 4, frágil,
    depende de <100s) vs HITL asíncrono (Día 5, robusto,
    responde al instante y notifica el resultado después)
  - Competencia AB-620: "Manage human-in-the-loop scenarios in agents"

- [x] Día 4 — Skills sustituyendo a Topics
  - Skill "Solicitar vacaciones" recoge datos incompletos preguntando
    lo que falta (días, motivo) — sin necesidad de nodos ni entidades
  - Flow ampliado con input `Dias` (número) + `Motivo` (texto)
  - Patrón final: skill recoge datos y dispara la herramienta sin
    intentar confirmar por su cuenta (poco fiable, LLM-based);
    el flow garantiza la seguridad real con aprobación humana
    determinista + respuesta asíncrona inmediata al agente
  - Sistema probado de extremo a extremo: conversación natural →
    extracción de datos → ejecución → aprobación → notificación

- [x] Día 5 — Conector estándar (SharePoint)
  - Lista "Políticas" en SharePoint conectada como herramienta
    "Obtener elementos" (Get items)
  - Parámetros clave: dirección de sitio y nombre de lista configurados
    como "Valor" fijo (no "IA"), porque son datos técnicos constantes,
    no información que aporta el usuario en la conversación
  - Ventaja frente a conocimiento estático (PDF, Día 3): los datos
    se leen en vivo, así que si alguien actualiza la lista de RRHH,
    el agente responde siempre con la información más reciente
  - Probado con éxito: respuesta exacta + cita de fuente con enlace
     
- [x] Día 6 — Prueba PostMan y creación de Conector personalizado con el agente.

- [x] Día 7 — Autenticación: API Key vs OAuth
  - API Key probada con éxito (exchangerate-api.com) — clave enviada
    como parámetro en la URL, respuesta 200 con datos en tiempo real
  - OAuth identificado retrospectivamente en el conector de SharePoint
    (Día 5): login + consentimiento en vez de clave suelta
  - Diferencia clave: API Key = clave fija sin identidad de usuario;
    OAuth = token temporal ligado a una identidad, con permisos
    limitados y revocación automática por caducidad
  - Nota de seguridad: nunca exponer la API Key real en capturas
    o repos públicos (se ha usado una key de prueba desechable)

- [x] Día 8 — Introducción a MCP
  - MCP = protocolo estándar para que un agente use herramientas de
    forma uniforme, en vez de una integración a medida por sistema
  - Catálogo real visto en Copilot Studio (28/08/2026... o la fecha
    de hoy): Calendar, Copilot, Fabric IQ Data MCP, Mail, Servidor MCP
    de Microsoft Dataverse, OneDrive, SharePoint, Teams, User,
    Work IQ (Preview), Azure - Foundry IQ, Azure Data Explorer,
    D365 Contact Center Admin MCP, Dynamics 365 Business Central
  - Varios sistemas (SharePoint, OneDrive, Teams) están disponibles
    tanto como conector clásico como servidor MCP — coexisten
  - "Work IQ" marcado como Preview → matiz de licencia/disponibilidad
    relevante para el examen

- [x] Día 9 — Introducción a MCP
  - Conectado servidor MCP de Microsoft Dataverse (OAuth con cuenta
    de usuario, no API Key)
  - Tool discovery confirmado: 4 herramientas expuestas automáticamente
    por el servidor sin definición manual: read_query, create_table,
    update_table, delete_table
  - Hallazgo de seguridad: delete_table exige "consentimiento explícito
    del usuario" en su propia descripción — MCP incorpora HITL para
    acciones destructivas por diseño, mismo principio que construimos
    manualmente en el Día 4-5 con el agent flow de aprobación
  - Diferencia clave con conector personalizado (Día 10): MCP = catálogo
    de herramientas ya definido por el proveedor; conector = tú defines
    cada acción a mano desde una API sin estándar

- [x] Día 10 — read_query en acción (Dataverse MCP)
  - Tabla Empleado creada con datos de prueba
  - HALLAZGO 1: el agente no elige una fuente de datos correcta sin
    instrucciones explícitas sobre qué contiene cada una (ya lo vimos)
  - HALLAZGO 2: nombre para mostrar ≠ nombre lógico en Dataverse.
    "Empleado" (display) vs "crfdb_empleado" (logical) — el prefijo
    depende del editor de solución del entorno. Mismo patrón aplica
    a columnas (crfdb_nombre, no "Nombre").
  - Instrucciones ajustadas con el nombre lógico correcto → consulta
    SQL generada automáticamente y respuesta exacta con cita de fuente
  - Competencia AB-620: "Configure and manage MCP tools" +
    depuración de esquema de datos real

- [x] Día 11
  Repaso + mini-simulacro (3.5/5) — Lección: no generalizar desde un único caso práctico a la regla del examen

- [x] Día 12 — Capacidades autónomas y desencadenadores por evento
  - Workflow (no Agent flow) con trigger "Cuando se crea un elemento"
    (SharePoint) — distinto del trigger "When an agent calls the flow"
  - Diferencia clave: agente reactivo (espera conversación) vs
    autónomo (se activa solo por evento externo)
  - Probado con éxito: nueva fila en SharePoint → email automático
    sin intervención humana en el chat
  - Riesgo identificado: sin límite de frecuencia, un alta masiva
    de filas dispararía múltiples emails sin control — mejora
    pendiente para diseño de producción real
  - Competencia AB-620: "Configure event-triggered automation"


## Proyectos
1. Agente de FAQ (en progreso)
2. Agente de soporte interno
3. Agente transaccional
4. Agente autónomo controlado
5. Solución empresarial completa (ALM)
