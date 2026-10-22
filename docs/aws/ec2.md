---
sidebar_position: 2
---

# EC2: cómputo en la nube

**Amazon EC2** (Elastic Compute Cloud) son máquinas virtuales en AWS. Aquí verás instancias, AMIs, security groups y cómo lanzar tu primer servidor.

---

## ¿Qué es EC2?

Una **instancia EC2** es un servidor virtual: CPU, RAM, disco y red, facturado por segundo o hora. Encaja en **IaaS**: tú eliges el SO, instalas software (por ejemplo Docker) y administras la máquina.

> Analogía: EC2 es “alquilar un PC en el datacenter de Amazon” y apagarlo cuando no lo necesitas.

---

## Conceptos clave

| Concepto | Descripción |
| -------- | ----------- |
| **AMI** | Imagen del sistema (Amazon Linux, Ubuntu, Windows). Es la “foto” desde la que nace la instancia. |
| **Instance type** | Tamaño: `t3.micro`, `t3.small`, `m5.large`… (vCPU y RAM). |
| **Key pair** | Par de claves SSH para entrar a Linux. Guarda el `.pem` en un lugar seguro. |
| **Security group** | Firewall virtual: qué puertos entran/salen (22, 80, 443…). |
| **VPC / subnet** | Red virtual y subred donde vive la instancia. |
| **EBS** | Disco persistente de la instancia. Si terminas la instancia, el volumen puede borrarse o no, según la opción. |
| **Elastic IP** | IP pública fija. Si no está asociada a una instancia **en ejecución**, se factura. |

---

## Tipos de instancia (resumen)

| Familia | Uso típico | Ejemplo |
| ------- | ---------- | ------- |
| **T** (burstable) | Prácticas, desarrollo, webs pequeñas | `t3.micro` (Free Tier) |
| **M** | Uso general | `m5.large` |
| **C** | Cómputo intensivo | `c6i.large` |
| **R** | Mucha memoria | `r6i.large` |

Para el curso: **`t2.micro` o `t3.micro`**.

---

## Lanzar una instancia (consola)

1. Entra a **EC2** → **Launch instance**.
2. Nombre: `curso-web`.
3. AMI: **Amazon Linux 2023** o **Ubuntu 24.04**.
4. Tipo: `t3.micro`.
5. Key pair: crea una y descarga el `.pem`.
6. Security group:
   - SSH (22) solo desde **tu IP**.
   - HTTP (80) desde `0.0.0.0/0` si vas a servir una web.
7. Almacenamiento: 8–20 GiB gp3 suele bastar.
8. **Launch**.

Cuando el estado sea **Running**, copia la **IP pública**.

---

## Conectarte por SSH

**Linux / macOS:**

```bash
chmod 400 curso-web.pem
ssh -i curso-web.pem ec2-user@IP_PUBLICA   # Amazon Linux
# ssh -i curso-web.pem ubuntu@IP_PUBLICA  # Ubuntu
```

**Windows:** usa PowerShell, Windows Terminal o un cliente SSH. El usuario depende de la AMI (`ec2-user` o `ubuntu`).

---

## Servidor web mínimo en la instancia

En Amazon Linux 2023:

```bash
sudo dnf update -y
sudo dnf install -y nginx
sudo systemctl enable --now nginx
echo "<h1>Hola desde EC2</h1>" | sudo tee /usr/share/nginx/html/index.html
```

Abre http://IP_PUBLICA (puerto 80 debe estar abierto en el security group).

---

## CLI: listar y parar

```bash
# Instancias en la región configurada
aws ec2 describe-instances \
  --query "Reservations[].Instances[].[InstanceId,State.Name,PublicIpAddress,Tags[?Key=='Name'].Value|[0]]" \
  --output table

# Parar (deja de cobrar cómputo; el EBS sigue facturando)
aws ec2 stop-instances --instance-ids i-0abc123

# Arrancar de nuevo
aws ec2 start-instances --instance-ids i-0abc123

# Terminar (elimina la instancia; revisa si el volumen se borra)
aws ec2 terminate-instances --instance-ids i-0abc123
```

:::tip Stop vs Terminate
**Stop:** la máquina se apaga; puedes volver a encenderla (puede cambiar la IP pública si no usas Elastic IP).  
**Terminate:** se destruye. Usa terminate al terminar la práctica.
:::

---

## Security groups: reglas típicas

| Tipo | Puerto | Origen | Cuándo |
| ---- | ------ | ------ | ------ |
| SSH | 22 | Tu IP `/32` | Administración |
| HTTP | 80 | `0.0.0.0/0` | Web pública |
| HTTPS | 443 | `0.0.0.0/0` | Web con TLS |
| App | 3000 | Security group del load balancer | No exponer la app al mundo |

Nunca abras `0.0.0.0/0` en el puerto 22.

---

## EC2 y Docker

En una instancia puedes instalar Docker y correr lo que ya viste en el módulo de contenedores:

```bash
sudo dnf install -y docker
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
# cierra sesión y vuelve a entrar
docker run -d -p 80:80 nginx:alpine
```

Más adelante, en producción, suele usarse **ECS**, **EKS** o un pipeline CI/CD en lugar de instalar todo a mano.

---

## Reto del día

1. Lanza una `t3.micro` con un security group que permita SSH (tu IP) y HTTP.
2. Instala Nginx (o un contenedor `nginx`) y sirve una página con tu nombre.
3. Haz una captura de `aws ec2 describe-instances` o de la consola con la instancia **Running**.
4. **Termina** la instancia y confirma que desaparece de la lista.
