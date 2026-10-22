---
sidebar_position: 1
---

# Conceptos básicos de AWS

Aprende qué es Amazon Web Services, cómo se organiza (regiones, cuentas, IAM) y por qué es el proveedor cloud que usaremos en el curso.

---

## ¿Por qué aprender AWS?

**AWS** es el proveedor de nube más usado. En el curso nos permite pasar de “tengo una app en Docker” a “la despliego y la opero en la nube”: servidores, almacenamiento, funciones serverless y arquitecturas que reaccionan a eventos.

> Con AWS no compras hardware: **alquilas servicios** (cómputo, disco, red, funciones) y pagas por lo que usas.

- ❌ Comprar un servidor, instalarlo y mantenerlo durante años.
- ✅ Crear un recurso, usarlo unas horas y apagarlo.

Consulta siempre la [documentación oficial de AWS](https://docs.aws.amazon.com/) para APIs, límites y precios actualizados.

---

## ¿Qué es AWS?

**Amazon Web Services** es una plataforma de más de 200 servicios en la nube. En este curso nos centramos en los que aparecen en los objetivos:

| Servicio | Tipo | Uso en el curso |
| -------- | ---- | --------------- |
| **EC2** | Cómputo (IaaS) | Máquinas virtuales (instancias) |
| **RDS** | Base de datos SQL (DBaaS) | MySQL / PostgreSQL gestionados |
| **DynamoDB** | Base de datos NoSQL | Tablas clave-valor (ej. catálogo) |
| **S3** | Almacenamiento | Objetos, archivos estáticos, backups |
| **Lambda** | Serverless (FaaS) | Código que se ejecuta ante un evento |
| **IAM** | Seguridad | Usuarios, roles y permisos |
| **Event-driven (extra)** | Arquitectura | S3 / colas → Lambda → DynamoDB |

---

## Cuenta, regiones y zonas de disponibilidad

### Cuenta AWS

Todo lo que creas pertenece a una **cuenta**. En el curso usa una cuenta de estudiante o Free Tier y **no subas claves** al repositorio.

### Región

Una **región** es un área geográfica (por ejemplo `us-east-1`, `sa-east-1`). Elige una y sé consistente: los recursos no se copian solos entre regiones.

Para Latinoamérica suele usarse:

- `us-east-1` (N. Virginia): más servicios y a menudo más barata.
- `sa-east-1` (São Paulo): menor latencia si tus usuarios están en Sudamérica.

### Zona de disponibilidad (AZ)

Dentro de una región hay varias **AZs** (datacenters independientes). Distribuir recursos en 2+ AZs mejora la disponibilidad.

```text
Cuenta AWS
 └── Región (ej. us-east-1)
      ├── AZ us-east-1a  → EC2, subred, etc.
      ├── AZ us-east-1b
      └── AZ us-east-1c
```

---

## IAM en 2 minutos

**IAM** (Identity and Access Management) controla **quién** puede hacer **qué**.

| Concepto | Qué es |
| -------- | ------ |
| **Usuario** | Identidad de una persona (consola o CLI). |
| **Grupo** | Conjunto de usuarios con los mismos permisos. |
| **Rol** | Identidad que asume un servicio (EC2, Lambda) para acceder a otros servicios. |
| **Política** | Documento JSON con permisos (`Allow` / `Deny`). |

Regla de oro: **mínimo privilegio**. No uses la cuenta root para el día a día; crea un usuario IAM con MFA.

---

## Consola vs CLI vs SDK

Puedes gestionar AWS de tres formas:

1. **Consola web:** interfaz gráfica para aprender y explorar.
2. **AWS CLI:** comandos en la terminal (automatizable).
3. **SDK:** librerías en Python, JavaScript, Java, etc. para tu código.

**Instalar y configurar la CLI:**

```bash
# macOS (Homebrew)
brew install awscli

# Comprobar
aws --version

# Configurar (Access Key + región)
aws configure
```

`aws configure` pide:

- Access Key ID
- Secret Access Key
- Default region (ej. `us-east-1`)
- Output format (`json` o `table`)

Comprobar identidad:

```bash
aws sts get-caller-identity
```

---

## Modelo de responsabilidad compartida

AWS opera los datacenters y el hipervisor; **tú** eres responsable de lo que pones encima (SO en EC2, datos, IAM, cifrado de aplicaciones).

| Capa | ¿Quién la gestiona? |
| ---- | ------------------- |
| Edificios, red física, hipervisor | AWS |
| Sistema operativo de una EC2 | Tú |
| Código de una Lambda | Tú (AWS gestiona el runtime) |
| Buckets S3 y permisos | Tú |
| Parches de RDS | AWS (tú configuras backups y acceso) |

---

## Free Tier y costes (importante)

El **Free Tier** cubre usos limitados (horas de EC2 t2/t3.micro, almacenamiento S3, invocaciones Lambda). Aun así:

- Apaga o termina instancias EC2 cuando no las uses.
- Borra volúmenes EBS huérfanos y Elastic IPs no asociadas.
- Activa **alertas de facturación** en la consola.

:::caution
Crear recursos y olvidarlos genera factura. Al terminar una práctica, limpia: instancias, IPs, buckets (si están vacíos) y funciones de prueba.
:::

---

## Resumen

- AWS = servicios bajo demanda (IaaS, PaaS, FaaS, almacenamiento).
- Trabajas en una **región**; las AZs dan alta disponibilidad.
- **IAM** define identidades y permisos.
- Consola para aprender; CLI/SDK para automatizar.
- Controla costes desde el primer día.
