# Cartrix AI System Documentation

## Arquitectura

Sistema compuesto por:

- n8n → Automatización y flujos
- PostgreSQL → Base de datos principal
- Qdrant → Base vectorial (RAG)
- OpenAI → IA (intención + respuestas)
- Notion → Gestión tareas
- Scripts Python → Integraciones
- Twilio → WhatsApp

---

## Flujo principal

1. Usuario envía mensaje WhatsApp
2. n8n recibe mensaje
3. OpenAI clasifica intención
4. Se consulta Qdrant (memoria)
5. Se genera respuesta
6. Se guarda en PostgreSQL

---

## Reglas del chatbot

### Consultas generales
- Responder usando conocimiento de cartrix.es

### Presupuestos
Preguntar uno a uno:
- Medidas (largo, ancho, alto)
- Cantidad
- Impresión exterior/interior
- Colores
- Tratamiento alimentario
- Acabados
- Localidad
- Tipo de caja

### Hablar con agente
- Avisar que será contactado
- Enviar correo y WhatsApp

### Pedido en curso
- Pedir número de pedido
- Escalar a humano

---

## Sistema RAG

- Qdrant almacena embeddings
- Se usa para recuperar contexto
- Solo contenido útil (sin cookies, legal, etc.)

---

## Base de datos

### usuarios
- id
- nombre
- telefono

### hilos_conversacion
- id
- usuario_id
- resumen
- intencion
- etiquetas
- idioma
- pais
- asignado_a

### mensajes
- id
- hilo_id
- contenido
- tipo
- fecha

### asignaciones
- hilo_id
- asignado_a
- asignado_por
- fecha

---

## Scripts

- sync_qdrant.py
- descargar_archivos_twilio.py
- importar_datos.py
- subir_presupuesto_y_actualizar.py

---

## Reglas técnicas

- UTF-8 obligatorio
- Evitar duplicados
- Limpiar texto antes de embeddings
- Mantener separación Cartrix / Colmar
