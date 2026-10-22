---
sidebar_position: 6
---

# Lambda: serverless

**AWS Lambda** ejecuta tu código sin que administres servidores. Pagas por invocación y duración. Aquí verás funciones, triggers y un ejemplo mínimo.

---

## ¿Qué es serverless (FaaS)?

En **Function as a Service** subes una **función**. AWS reserva capacidad, la ejecuta cuando llega un evento y la apaga. No hay una EC2 que parchear 24/7.

> Analogía: en EC2 alquilas el restaurante entero; en Lambda pagas **solo cada plato que se cocina**.

| EC2 | Lambda |
| --- | ------ |
| Servidor siempre encendido (o lo arrancas tú) | Se ejecuta al llegar un evento |
| Escalas instancias | Escala el número de invocaciones |
| Pagas por hora/segundo de máquina | Pagas por invocaciones y GB-segundo |
| Tú instalas runtime y parches | AWS gestiona el runtime |

Límites útiles: timeout máximo 15 minutos; memoria de 128 MB a 10 GB. No sirve para procesos que deban correr días.

---

## Anatomía de una función

| Pieza | Descripción |
| ----- | ----------- |
| **Handler** | Función de entrada (`index.handler`). |
| **Runtime** | Node.js, Python, Java, Go, etc. |
| **Trigger** | Quién la invoca: API Gateway, S3, EventBridge, SQS, HTTP Function URL… |
| **Rol IAM** | Permisos de la función (escribir logs, leer S3…). |
| **Timeout / memoria** | Límites de ejecución. |
| **Variables de entorno** | Configuración (nunca secretos en texto plano en producción). |

Flujo:

```text
Evento (subida a S3, HTTP, cron)
    → Lambda ejecuta el handler
        → (opcional) llama a S3, DynamoDB, otro API
            → respuesta o fin
```

---

## Ejemplo: función Python

Handler que recibe un evento y devuelve JSON:

```python
def handler(event, context):
    nombre = "mundo"
    if isinstance(event, dict):
        nombre = event.get("nombre", nombre)
    return {
        "statusCode": 200,
        "body": f"Hola, {nombre}"
    }
```

En Node.js el equivalente sería `exports.handler = async (event) => { ... }`.

---

## Crear e invocar con la CLI (visión general)

1. Empaqueta el código en un ZIP.
2. Crea un **rol IAM** que Lambda pueda asumir, con al menos permiso de logs en CloudWatch.
3. Crea la función:

```bash
aws lambda create-function \
  --function-name hola-curso \
  --runtime python3.12 \
  --role arn:aws:iam::CUENTA:role/lambda-basico \
  --handler index.handler \
  --zip-file fileb://funcion.zip
```

Invocar:

```bash
aws lambda invoke \
  --function-name hola-curso \
  --payload '{"nombre":"Pepe"}' \
  --cli-binary-format raw-in-base64-out \
  respuesta.json

cat respuesta.json
```

Desde la consola: **Lambda** → **Create function** → **Author from scratch**. Es más visual para la primera vez.

---

## Triggers frecuentes en el curso

| Trigger | Evento | Ejemplo |
| ------- | ------ | ------- |
| **Function URL** o **API Gateway** | HTTP | API REST/HTTP sin servidor |
| **S3** | ObjectCreated | Miniatura al subir una imagen |
| **EventBridge (cron)** | Programado | Tarea cada noche |
| **SQS** | Mensaje en cola | Procesar trabajos en segundo plano |
| **DynamoDB Streams** | Cambio en una tabla | Reaccionar a un alta |

---

## Permisos: el rol lo es todo

La función **no** usa tu usuario IAM. Usa un **rol**. Si Lambda debe leer un bucket:

- El rol necesita `s3:GetObject` sobre ese bucket.
- El bucket no debe impedir el acceso (política del bucket).

Logs: sin `logs:CreateLogGroup` / `CreateLogStream` / `PutLogEvents` no verás salida en CloudWatch.

---

## Cold start

La primera invocación (o tras un rato sin tráfico) puede tardar más: AWS prepara el entorno. En prácticas del curso no suele ser un problema; en APIs de baja latencia se mitiga con provisioned concurrency o runtimes ligeros.

---

## Reto del día

1. Crea una función `hola-curso` (consola o CLI) que reciba `nombre` y responda un saludo.
2. Invócala con un payload de prueba y guarda la respuesta.
3. Revisa los logs en **CloudWatch** → **Log groups** → `/aws/lambda/hola-curso`.
4. (Opcional) Añade una Function URL y llama la función con `curl`.
5. Elimina la función si era solo de prueba:

```bash
aws lambda delete-function --function-name hola-curso
```

---

## Workshop relacionado

Para un trigger real (CSV en S3 → validar → DynamoDB), sigue [Carga automática de datos con Amazon S3, AWS Lambda y Amazon DynamoDB](./workshops/carga-csv-s3-lambda-dynamodb.md).
