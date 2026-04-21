# Cartrix AI System – Documentación Completa

---

# 🧠 VISIÓN GENERAL

Sistema automatizado de atención al cliente vía WhatsApp para Cartrix, con capacidades de IA, memoria semántica (RAG) y gestión interna de conversaciones.

El sistema permite:
- Responder automáticamente a clientes
- Generar presupuestos guiados
- Escalar a humanos cuando es necesario
- Guardar y analizar todas las conversaciones

---

# 🏗️ ARQUITECTURA DEL SISTEMA

## Componentes principales

### 1. n8n (Orquestador)
- Recibe mensajes desde Twilio (WhatsApp)
- Ejecuta lógica del chatbot
- Conecta con OpenAI, PostgreSQL y Qdrant
- Gestiona triggers y automatizaciones

---

### 2. PostgreSQL (Base de datos principal)
- Almacena toda la información estructurada
- Conversaciones, usuarios, mensajes, asignaciones

---

### 3. Qdrant (Memoria vectorial - RAG)
- Guarda embeddings de:
  - Conversaciones
  - Contenido web (cartrix.es)
- Permite recuperación semántica

---

### 4. OpenAI
- Clasificación de intención
- Generación de respuestas
- Generación de embeddings
- Limpieza y optimización de contenido

---

### 5. Twilio (WhatsApp)
- Canal de entrada/salida de mensajes
- Manejo de multimedia

---

### 6. Scripts Python

- `sync_qdrant.py`
  - Sincroniza PostgreSQL → Qdrant

- `descargar_archivos_twilio.py`
  - Descarga archivos multimedia desde Twilio

- `importar_datos.py`
  - Conecta Excel + Notion + DB

- `subir_presupuesto_y_actualizar.py`
  - Sube PDFs a Drive y actualiza Notion

---

### 7. Notion
- Gestión de tareas
- Seguimiento de presupuestos

---

# 🔄 FLUJO PRINCIPAL (WHATSAPP)

1. Cliente envía mensaje por WhatsApp
2. Twilio → n8n (Webhook)
3. n8n consulta PostgreSQL (usuario/hilo)
4. OpenAI clasifica intención
5. Se consulta Qdrant (memoria contextual)
6. OpenAI genera respuesta
7. n8n envía respuesta por Twilio
8. Se guarda todo en PostgreSQL
9. (Opcional) Sync a Qdrant

---

# 🧠 SISTEMA RAG (MEMORIA)

## Funcionamiento

1. Se genera embedding del mensaje
2. Se consulta Qdrant
3. Se recuperan mensajes/contexto relevante
4. Se envía a OpenAI junto con el prompt
5. Se genera respuesta enriquecida

## Reglas

- SOLO contenido útil
- EXCLUIR:
  - Cookies
  - Política privacidad
  - Textos legales

---

# 🤖 CLASIFICACIÓN DE INTENCIONES

Principales tipos:

- consulta_general
- presupuesto
- estado_pedido
- hablar_con_agente
- envio_archivos
- desconocido

---

# 📋 LÓGICA DEL CHATBOT

## 1. Consulta general
- Responde usando conocimiento de cartrix.es
- Explica tipos de cajas

---

## 2. Presupuesto (flujo guiado)

Preguntar SIEMPRE uno a uno:

1. Largo
2. Ancho
3. Alto
4. Cantidad
5. Impresión exterior (sí/no + tintas)
6. Impresión interior (sí/no + tintas)
7. Colores iguales (sí/no)
8. Tratamiento alimentario
9. Acabado especial
10. Localidad de entrega
11. Tipo de caja / uso

👉 Al finalizar:
- Enviar correo a oficina técnica
- Guardar datos

---

## 3. Hablar con agente
- Confirmar al cliente
- Enviar:
  - Email
  - WhatsApp interno
- Incluir conversación completa

---

## 4. Pedido en curso
- Pedir número de pedido
- Escalar a humano

---

## 5. Fallback
- Si no entiende:
  - Escalar a humano

---

# 🌍 MULTIIDIOMA

- Detectar idioma automáticamente
- Soporte:
  - Español
  - Inglés
  - Francés
  - Catalán
- Responder en el idioma del cliente

---

# 🗂️ BASE DE DATOS (ESTRUCTURA REAL)

## usuarios

- id
- nombre
- numero_telefono

---

## hilos_conversacion

- id
- usuario_id
- resumen
- intencion
- etiquetas
- idioma
- pais
- asignado_a
- gestionado (boolean)
- fecha_inicio
- notion_page_id

---

## mensajes

- id
- hilo_id
- contenido
- tipo (usuario / bot)
- fecha
- archivo_url (opcional)

---

## asignaciones

- hilo_id
- asignado_a
- asignado_por
- fecha

---

# 🧠 REGLAS DE NEGOCIO

- Nunca dar precios directos
- Siempre guiar hacia presupuesto estructurado
- Priorizar respuestas cortas tipo WhatsApp
- Detectar intención antes de responder
- Escalar a humano si hay duda

---

# ⚙️ REGLAS TÉCNICAS

- UTF-8 obligatorio
- Evitar duplicados en Qdrant
- Limpiar texto antes de embeddings
- No perder historial de conversaciones
- Mantener separación Cartrix / Colmar

---

# ⚠️ ERRORES CONOCIDOS

- Twilio:
  - Error: "A text message body must be specified"
  - Causa: mensaje vacío

- PostgreSQL:
  - Campo `conversacion_id` no definido

- Encoding:
  - Problemas UTF-8 (acentos)

---

# 🚀 OBJETIVOS FUTUROS

- Integración completa RAG en tiempo real
- Mejora de clasificación de intención
- Auto-etiquetado de conversaciones
- Envío automático de PDFs de resumen
- Integración con ERP / Odoo

---

# 🧩 FILOSOFÍA DEL SISTEMA

Sistema diseñado para:
- Automatizar sin perder control humano
- Escalar conversaciones importantes
- No perder ningún lead
- Ofrecer respuestas naturales (no robóticas)
