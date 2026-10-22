---
sidebar_position: 2
---

# Fundamentos Cloud

Esta página introduce los conceptos esenciales de la computación en la nube: su evolución, la definición y características según NIST, y los modelos de servicio.

---

## Evolución hacia Cloud

La computación en la nube es el resultado de una evolución técnica y de negocio a lo largo de décadas:

1. **Mainframes y terminales** — Un único ordenador central y usuarios conectados por terminales.
2. **Cliente-servidor** — Servidores en la empresa y PCs en los puestos de trabajo.
3. **Virtualización** — Uso de máquinas virtuales (VMs) para aprovechar mejor el hardware y aislar cargas de trabajo.
4. **Grid computing** — Recursos distribuidos para tareas de alto cómputo (científico, renderizado).
5. **Cloud computing** — Recursos (cómputo, almacenamiento, red) ofrecidos como **servicio** bajo demanda, escalables y gestionados por un proveedor.

Factores que impulsaron el cambio:
- Necesidad de **escalar** rápido sin grandes inversiones iniciales.
- Pago por **uso** (CapEx → OpEx).
- **Automatización** y APIs para aprovisionar y gestionar recursos.
- **Resiliencia** y redundancia geográfica de los grandes proveedores.

---

## Características NIST

El **NIST** (National Institute of Standards and Technology) define la computación en la nube mediante cinco características esenciales:

### 1. Autoservicio bajo demanda (On-demand self-service)
El usuario puede provisionar recursos (por ejemplo, servidores, almacenamiento) sin interacción humana con el proveedor. Todo se hace mediante portales web o APIs.

### 2. Acceso amplio a la red (Broad network access)
Los recursos están disponibles en la red y se accede mediante mecanismos estándar (navegador, APIs, clientes ligeros), desde distintos dispositivos (móvil, tablet, portátil, escritorio).

### 3. Agrupación de recursos (Resource pooling)
El proveedor usa un modelo **multi-tenant**: los recursos (cómputo, almacenamiento, memoria, red) se agrupan y se asignan dinámicamente a múltiples consumidores, con cierto aislamiento lógico. El usuario normalmente no controla ni conoce la ubicación exacta de los recursos.

### 4. Elasticidad rápida (Rapid elasticity)
Los recursos pueden aprovisionarse y liberarse de forma ágil, en algunos casos de forma automática, para escalar hacia fuera o hacia dentro según la demanda. Para el usuario los recursos parecen ilimitados.

### 5. Servicio medido (Measured service)
Los sistemas en la nube controlan y optimizan el uso de recursos mediante medición (por ejemplo, almacenamiento, procesamiento, ancho de banda, cuentas de usuario). El uso es transparente tanto para el proveedor como para el consumidor (facturación, control de costes).

---

## Modelos de servicio

En cloud, el consumidor asume más o menos responsabilidad según el **nivel de abstracción** del servicio.

### IaaS — Infrastructure as a Service
- **Qué ofrece:** Cómputo (VMs), almacenamiento, red. Tú gestionas el SO, middleware, aplicaciones y datos.
- **Ejemplos:** Amazon EC2, Google Compute Engine, Azure Virtual Machines, OpenStack.
- **Uso típico:** Migrar cargas existentes, control total del stack, lift-and-shift.

### PaaS — Platform as a Service
- **Qué ofrece:** Plataforma para desarrollar, desplegar y ejecutar aplicaciones. El proveedor gestiona infraestructura y entorno de ejecución (runtime, bases de datos gestionadas). Tú te centras en el código y la configuración de la aplicación.
- **Ejemplos:** AWS Elastic Beanstalk, Google App Engine, Azure App Service, Heroku.
- **Uso típico:** Desarrollo ágil, menos gestión de servidores y parches.

### SaaS — Software as a Service
- **Qué ofrece:** Aplicación lista para usar en la nube. El proveedor gestiona todo (infraestructura, plataforma, aplicación). Accedes vía navegador o API.
- **Ejemplos:** Gmail, Salesforce, Slack, Microsoft 365, Notion.
- **Uso típico:** Productividad, CRM, colaboración, sin instalación ni mantenimiento.

### Otros modelos
- **FaaS** (Function as a Service): Ejecución de funciones (event-driven, serverless), p. ej. AWS Lambda, Google Cloud Functions.
- **CaaS** (Container as a Service): Orquestación de contenedores gestionada, p. ej. AWS ECS, Google GKE, Azure AKS.
- **DBaaS**: Bases de datos gestionadas (RDS, Cloud SQL, Cosmos DB).

En resumen: a mayor nivel de servicio (hacia SaaS), menos control sobre la infraestructura y menos tareas de administración; a menor nivel (hacia IaaS), más control y más responsabilidad de gestión.

---

## Siguiente: práctica en AWS

Con estos fundamentos, el módulo **[AWS](./aws/conceptos-basicos.md)** aplica **EC2**, **RDS**, **DynamoDB**, **S3** y **Lambda**, más arquitectura event-driven como extra.
