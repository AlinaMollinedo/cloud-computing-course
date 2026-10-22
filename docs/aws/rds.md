---
sidebar_position: 3
---

# RDS: bases de datos relacionales

**Amazon RDS** (Relational Database Service) es una base de datos **SQL gestionada**: tú eliges el motor y el tamaño; AWS instala, parchea, hace backups y puede replicar en otra AZ.

---

## ¿Qué es RDS?

En EC2 podrías instalar MySQL a mano. Con RDS el modelo es **DBaaS**: no entras al SO del servidor de base de datos; te conectas con un cliente (`mysql`, `psql`, DBeaver) usando endpoint, puerto, usuario y contraseña.

> Analogía: EC2 es alquilar la cocina entera; RDS es un restaurante donde pides el motor (MySQL, PostgreSQL…) y AWS se ocupa de la cocina.

---

## Motores habituales

| Motor | Uso típico en el curso |
| ----- | ---------------------- |
| **MySQL** | Apps web clásicas, WordPress |
| **MariaDB** | Compatible con MySQL |
| **PostgreSQL** | Apps nuevas, tipos ricos, JSONB |
| **Aurora** | Compatible MySQL/PostgreSQL, más rendimiento (suele costar más) |
| **SQL Server / Oracle** | Legado empresarial (licencias) |

Para prácticas: **MySQL** o **PostgreSQL**, clase `db.t3.micro` (Free Tier, si aplica).

---

## Conceptos clave

| Concepto | Descripción |
| -------- | ----------- |
| **DB instance** | La instancia gestionada (CPU, RAM, almacenamiento). |
| **Engine + version** | Ej. MySQL 8.0, PostgreSQL 16. |
| **Endpoint** | Hostname para conectar, ej. `tienda.xxxx.us-east-1.rds.amazonaws.com`. |
| **Puerto** | 3306 (MySQL/MariaDB), 5432 (PostgreSQL). |
| **Subnet group** | Subredes donde vive RDS (normalmente privadas). |
| **Security group** | Quién puede abrir el puerto de la base (ideal: solo el SG de EC2 o tu IP). |
| **Storage** | Disco gp3; crece aparte del cómputo. |
| **Backup automático** | Retención (días) y ventana de backup. |
| **Multi-AZ** | Réplica síncrona en otra AZ (alta disponibilidad; no es “más rápido”). |
| **Read replica** | Réplica asíncrona para lecturas. |

---

## RDS vs EC2 con MySQL vs DynamoDB

| | **RDS** | **MySQL en EC2** | **DynamoDB** |
| --- | ------- | ---------------- | ------------ |
| Modelo | SQL relacional gestionado | SQL, tú administras el SO | NoSQL clave-valor |
| Parches / backups | AWS | Tú | AWS |
| Esquema | Tablas, FK, SQL | Igual | Ítems, PK (y SK opcional) |
| Escalar | Vertical (+ réplicas) | Tú | Horizontal, on-demand |
| Cuándo | Pedidos, usuarios, joins | Control total / software raro | Catálogos, sesiones, IoT |

---

## Crear una instancia (consola)

1. **RDS** → **Create database**.
2. **Standard create**.
3. Engine: **MySQL** (o PostgreSQL).
4. Templates: **Free tier** si aparece.
5. DB instance identifier: `tienda-db`.
6. Master username: `admin` (no uses `root` como hábito en producción).
7. Master password: genera una y **guárdala** (no la subas a Git).
8. Instance: `db.t3.micro`.
9. Storage: gp3, 20 GiB; desactiva autoscaling si es solo el lab.
10. Conectividad:
    - VPC por defecto está bien para el curso.
    - **Public access:** Yes solo si te conectas desde tu portátil; **No** si solo entra una EC2 en la misma VPC (mejor).
    - Security group: crea uno `rds-tienda` que permita **3306** (o 5432) desde el SG de tu EC2 o desde **tu IP**, nunca `0.0.0.0/0`.
11. DB name inicial: `tienda`.
12. **Create database**. Espera a que el estado sea **Available** (varios minutos).

Copia el **Endpoint**.

:::caution
Una `db.t3.micro` encendida todo el mes genera factura si sales del Free Tier. **Para** o **elimina** la instancia al terminar la práctica.
:::

---

## Conectar

Desde tu máquina (si el SG lo permite y Public access = Yes):

```bash
# MySQL
mysql -h ENDPOINT -P 3306 -u admin -p tienda

# PostgreSQL
psql -h ENDPOINT -p 5432 -U admin -d tienda
```

Desde una **EC2** en la misma VPC (patrón recomendado): el SG de RDS acepta el puerto solo desde el SG de la instancia. En la EC2:

```sql
CREATE TABLE productos (
  producto_id VARCHAR(16) PRIMARY KEY,
  nombre VARCHAR(120) NOT NULL,
  categoria VARCHAR(80) NOT NULL,
  precio DECIMAL(10, 2) NOT NULL,
  stock INT NOT NULL
);

INSERT INTO productos VALUES
  ('P001', 'Teclado', 'Accesorios', 150.00, 20),
  ('P002', 'Mouse', 'Accesorios', 85.50, 35);

SELECT * FROM productos;
```

---

## CLI útil

```bash
aws rds describe-db-instances \
  --query "DBInstances[].[DBInstanceIdentifier,DBInstanceStatus,Endpoint.Address]" \
  --output table

# Parar (no todas las clases/motores; revisa la consola)
aws rds stop-db-instance --db-instance-identifier tienda-db

# Eliminar (crea snapshot final si lo pides)
aws rds delete-db-instance \
  --db-instance-identifier tienda-db \
  --skip-final-snapshot
```

---

## Seguridad mínima

- Contraseña en un gestor o en **Secrets Manager**, no en el código.
- Puerto de la BD cerrado a internet.
- Cifrado en reposo (AES-256) activado al crear.
- Backups con retención ≥ 1 día en cualquier entorno que no sea un lab de 20 minutos.

---

## Reto del día

1. Crea `tienda-db` (`db.t3.micro`, MySQL o PostgreSQL).
2. Abre el puerto **solo** desde tu IP o desde una EC2.
3. Crea la tabla `productos`, inserta dos filas y haz `SELECT`.
4. Captura el endpoint y el resultado del `SELECT`.
5. **Elimina** la instancia (y el security group si lo creaste solo para esto).
