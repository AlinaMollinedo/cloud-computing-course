---
sidebar_position: 7
sidebar_label: Event-driven (extra)
---

# Arquitectura event-driven (extra)

Módulo opcional: cómo encajan **S3**, **Lambda** y **DynamoDB** (y colas) cuando la app **reacciona a eventos** en lugar de a una cadena síncrona.

En la nube muchas aplicaciones no se llaman en cadena: reaccionan a un archivo subido, un pago, un cron o un mensaje en cola.

---

## ¿Qué es event-driven?

Un **evento** es un hecho que ya ocurrió: “se creó un objeto en S3”, “llegó un HTTP POST”, “son las 03:00”. Los **productores** emiten eventos; los **consumidores** reaccionan, a menudo de forma asíncrona.

```text
Productor  →  Evento  →  Bus / cola / notificación  →  Consumidor(es)
   S3           PutObject         EventBridge / SQS         Lambda
```

Ventajas:

- **Desacoplamiento:** S3 no necesita conocer el código que genera miniaturas.
- **Escala por evento:** si suben 1000 archivos, se lanzan 1000 invocaciones (con límites).
- **Resiliencia:** las colas reintentan si el consumidor falla.

---

## Piezas de AWS que encajan

| Servicio | Rol |
| -------- | --- |
| **S3** | Emite eventos al crear/borrar objetos. |
| **Lambda** | Consumidor serverless. |
| **EventBridge** | Bus de eventos y reglas (cron, eventos de servicios AWS, eventos custom). |
| **SNS** | Pub/sub: un mensaje, muchos suscriptores. |
| **SQS** | Cola: un mensaje, un consumidor (con visibilidad y reintentos). |
| **API Gateway** | Convierte HTTP en un evento para Lambda. |
| **DynamoDB Streams** | Eventos de cambios en una tabla. |

Patrón habitual de desacople:

```text
API / app  →  SNS o EventBridge  →  SQS  →  Lambda / EC2 workers
```

SNS notifica a muchos; SQS absorbe picos y evita perder mensajes.

---

## Ejemplo 1: S3 → Lambda

Objetivo: al subir una imagen a `s3://curso-uploads/`, una Lambda escribe un log o copia metadatos.

1. Bucket `curso-uploads`.
2. Función Lambda con permiso de lectura sobre ese bucket.
3. En el bucket: **Event notifications** → evento `PUT` → destino la Lambda.
4. AWS añade permiso de invocación (`lambda:InvokeFunction`) desde S3.

El `event` en Python trae `Records[].s3.bucket.name` y `object.key`.

---

## Ejemplo 2: cron con EventBridge

Objetivo: cada día a las 08:00 (UTC) ejecutar una función que genere un reporte.

En EventBridge:

- Rule tipo **schedule**: `cron(0 8 * * ? *)` o rate `rate(1 day)`.
- Target: la Lambda.

No hay servidor esperando: solo corre cuando toca el horario.

---

## Ejemplo 3: HTTP event-driven

```text
Cliente  →  API Gateway o Function URL  →  Lambda  →  DynamoDB / S3
```

El “evento” es la petición HTTP. La función es **stateless**: el estado vive en S3, DynamoDB o RDS, no en memoria de un servidor.

---

## Síncrono vs asíncrono

| Invocación | Comportamiento | Ejemplo |
| ---------- | -------------- | ------- |
| **Síncrona** | El llamador espera la respuesta | API Gateway → Lambda |
| **Asíncrona** | Fire-and-forget; reintentos automáticos | S3 → Lambda, EventBridge → Lambda |
| **Cola** | El trabajador hace polling o se dispara por evento | SQS → Lambda |

Si el trabajo es largo o puede fallar, **asíncrono + SQS** suele ser más robusto que encadenar HTTP.

---

## Buenas prácticas

- Eventos **pequeños** (IDs, claves S3), no payloads enormes en el mensaje.
- Idempotencia: procesar dos veces el mismo evento no debe duplicar efectos (usa IDs).
- DLQ (**dead-letter queue**) para mensajes que fallan siempre.
- Un productor no debería llamar a diez Lambdas a mano: usa el bus o la cola.

---

## Reto del día

Diseña (en un diagrama o README) **una** de estas arquitecturas y explica el flujo:

1. Subida de CV a S3 → Lambda extrae texto → guarda resultado en otro prefijo S3.
2. API de “crear pedido” → SQS → Lambda que procesa el pedido.
3. EventBridge cron cada hora → Lambda que lista un bucket y envía un resumen (aunque el “envío” sea solo un log en CloudWatch).

Indica: productor, evento, canal y consumidor. No hace falta desplegar las tres; con una implementada basta para la práctica.

El workshop [Carga automática de datos con Amazon S3, AWS Lambda y Amazon DynamoDB](./workshops/carga-csv-s3-lambda-dynamodb.md) es el mismo patrón (S3 → Lambda → DynamoDB) paso a paso.
