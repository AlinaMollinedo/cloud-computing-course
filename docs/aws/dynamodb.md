---
sidebar_position: 4
---

# DynamoDB: base de datos NoSQL

**Amazon DynamoDB** es una base de datos **NoSQL** gestionada, de clave-valor (y documentos). No hay servidores que parchear: creas una tabla y pagas por almacenamiento y requests.

---

## ¿Qué es DynamoDB?

A diferencia de RDS (filas, columnas, SQL, joins), en DynamoDB guardas **ítems** identificados por una **clave de partición** (y, si hace falta, una **clave de ordenación**). Escala de forma horizontal: el catálogo de una tienda o sesiones de usuario encajan bien.

> Analogía: RDS es una hoja de cálculo con relaciones entre hojas; DynamoDB es un archivador: cada ficha se busca por su identificador.

El workshop [Carga automática de datos con S3, Lambda y DynamoDB](./workshops/carga-csv-s3-lambda-dynamodb.md) usa una tabla `Productos` con `productoId` como clave.

---

## Conceptos clave

| Concepto | Descripción |
| -------- | ----------- |
| **Tabla** | Contenedor de ítems. |
| **Ítem** | Un registro (documento). Puede tener atributos distintos al de al lado. |
| **Atributo** | Un campo: String, Number, Boolean, Map, List… |
| **Partition key (PK)** | Obligatoria. Identifica el ítem (o el grupo si hay sort key). |
| **Sort key (SK)** | Opcional. Varios ítems con la misma PK, ordenados por SK. |
| **Primary key** | Solo PK, o PK + SK. |
| **On-demand** | Pagas por request; ideal para labs y tráfico irregular. |
| **Provisioned** | RCU/WCU reservadas; útil si el tráfico es estable. |
| **Query** | Lectura por PK (y rango de SK). Eficiente. |
| **Scan** | Recorre la tabla entera. Caro; evítalo en producción. |

Ejemplo de ítem (tabla `Productos`):

```json
{
  "productoId": "P001",
  "nombre": "Teclado",
  "categoria": "Accesorios",
  "precio": 150.00,
  "stock": 20
}
```

---

## DynamoDB vs RDS

| Pregunta | Si la respuesta es sí… | Servicio |
| -------- | ---------------------- | -------- |
| ¿Necesito joins, transacciones SQL, reportes ad hoc? | Relacional | **RDS** |
| ¿Acceso por ID, alto volumen, esquema flexible? | Clave-valor | **DynamoDB** |
| ¿Catálogo cargado desde CSV por eventos? | Ítems por `productoId` | **DynamoDB** (ver workshop) |
| ¿Pedidos con stock y varias tablas relacionadas? | SQL | **RDS** |

Puedes usar **ambos** en la misma app: DynamoDB para el catálogo de lectura rápida, RDS para facturación.

---

## Crear la tabla `Productos`

1. **DynamoDB** → **Tables** → **Create table**.
2. Table name: `Productos`.
3. Partition key: `productoId`, tipo **String**.
4. Sin sort key.
5. Capacity: **On-demand**.
6. **Create table**. Espera **Active**.

```bash
aws dynamodb create-table \
  --table-name Productos \
  --attribute-definitions AttributeName=productoId,AttributeType=S \
  --key-schema AttributeName=productoId,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST
```

---

## Escribir y leer (CLI)

```bash
# Insertar / sobrescribir
aws dynamodb put-item \
  --table-name Productos \
  --item '{
    "productoId": {"S": "P001"},
    "nombre": {"S": "Teclado"},
    "categoria": {"S": "Accesorios"},
    "precio": {"N": "150.00"},
    "stock": {"N": "20"}
  }'

# Leer por clave
aws dynamodb get-item \
  --table-name Productos \
  --key '{"productoId": {"S": "P001"}}'

# Recorrer (lab; no lo uses como “SELECT *” en producción)
aws dynamodb scan --table-name Productos --output table
```

En la consola: tabla → **Explore items** → **Create item**.

:::tip Números
En la API de DynamoDB el precio es tipo **N** (Number). Desde Python (`boto3`) usa `Decimal`, no `float`.
:::

---

## Query vs Scan

```bash
# Query: necesitas la PK (aquí la PK es el propio producto)
aws dynamodb query \
  --table-name Productos \
  --key-condition-expression "productoId = :id" \
  --expression-attribute-values '{":id": {"S": "P001"}}'
```

Para listar “todos los Accesorios” **no** basta la PK `productoId`. Opciones:

- Un **índice secundario** (GSI) con `categoria` como PK, o
- Hacer el filtro en la aplicación, o
- Modelar otra tabla / otra clave (p. ej. PK = categoria, SK = productoId).

El modelado se diseña **según las consultas**, no al revés.

---

## Permisos (IAM)

Una Lambda o un usuario solo necesita lo que usa, por ejemplo:

- `dynamodb:PutItem`, `GetItem`, `Query` sobre `arn:aws:dynamodb:REGION:CUENTA:table/Productos`.

`Scan` y `DeleteTable` no deberían estar en el rol de la app.

---

## DynamoDB Streams (preview)

Si activas **Streams**, cada alta/baja/cambio puede disparar una Lambda. Encaja con [arquitectura event-driven](./arquitectura-event-driven.md). En el curso el patrón principal del workshop es **S3 → Lambda → PutItem**, no Streams.

---

## Limpieza

```bash
aws dynamodb delete-table --table-name Productos
```

On-demand cobra almacenamiento y requests; una tabla de prueba vacía es barata, pero **bórrala** igual al terminar.

---

## Reto del día

1. Crea `Productos` con PK `productoId`.
2. Inserta P001, P002 y P003 (los del CSV del workshop).
3. `get-item` de P001 y un `scan`; captura ambas salidas.
4. Intenta un `put-item` sin `productoId` y anota el error.
5. Elimina la tabla, o déjala si vas a hacer el [workshop de carga CSV](./workshops/carga-csv-s3-lambda-dynamodb.md) a continuación.
