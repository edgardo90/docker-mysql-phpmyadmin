# docker-mysql-phpmyadmin

# 📘 Guía Completa de Configuración  
## Docker, MySQL y phpMyAdmin en AWS EC2

---

## 📌 Información General

### Entorno probado
- Amazon Linux 2023
- Amazon Linux 2
- Instancia EC2 (AWS)

### Objetivo
Desplegar de forma **automatizada**, **ordenada** y **reproducible** una base de datos **MySQL 8** junto con una interfaz web **phpMyAdmin**, utilizando **Docker y Docker Compose** sobre una instancia EC2.

Este enfoque evita instalaciones manuales, reduce errores, mejora la seguridad y permite replicar el entorno fácilmente.

---

## 🧱 Arquitectura General

```
┌──────────────────────┐
│     Navegador        │
│ http://IP_EC2:8080   │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│    phpMyAdmin        │
│  Contenedor Docker   │
│  Puerto interno 80   │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│      MySQL 8         │
│  Contenedor Docker   │
│  Puerto interno 3306 │
└──────────────────────┘
```

---

## 1️⃣ Instalación y Configuración de Docker

Docker será el motor encargado de ejecutar los contenedores de MySQL y phpMyAdmin.

### 1.1 Actualizar el sistema

```bash
sudo dnf update -y
```

### 1.2 Instalar Docker

```bash
sudo dnf install docker -y
```

### 1.3 Iniciar el servicio Docker

```bash
sudo systemctl start docker
```

### 1.4 Habilitar Docker al iniciar el sistema

```bash
sudo systemctl enable docker
```

### 1.5 Agregar el usuario al grupo Docker

```bash
sudo usermod -aG docker ec2-user
```

### 1.6 Aplicar permisos de usuario

```bash
exit
```

Cerrar la sesión SSH y volver a conectarse a la instancia.

---

## 2️⃣ Instalación de Docker Compose

Docker Compose permite definir y administrar múltiples contenedores desde un único archivo.

### 2.1 Instalar Docker Compose Plugin

```bash
sudo dnf install docker-compose-plugin -y
```

### 2.2 Verificar instalación

```bash
docker compose version
```

---

## 3️⃣ Creación del Entorno de Base de Datos

### 3.1 Crear carpeta del proyecto

```bash
mkdir servidor-datos
cd servidor-datos
```

### 3.2 Crear archivo docker-compose.yml

```bash
nano docker-compose.yml
```

### 3.3 Contenido del archivo docker-compose.yml

```yaml
version: '3.8'

services:
  db:
    image: mysql:8.0
    container_name: mysql_db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: tu_password_aqui
      MYSQL_DATABASE: mi_base_datos
    ports:
      - "3306:3306"

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: pma_web
    restart: always
    depends_on:
      - db
    ports:
      - "8080:80"
    environment:
      PMA_HOST: db
      MYSQL_ROOT_PASSWORD: tu_password_aqui
```

---

## 4️⃣ Despliegue del Entorno

### 4.1 Levantar los contenedores

```bash
docker compose up -d
```

### 4.2 Ver contenedores activos

```bash
docker ps
```

### 4.3 Ver logs

```bash
docker compose logs -f
```

### 4.4 Detener y eliminar contenedores

```bash
docker compose down
```

---

## 5️⃣ Configuración de Red en AWS (Security Group)

Regla de entrada necesaria:

| Campo | Valor |
|------|------|
| Type | Custom TCP |
| Port Range | 8080 |
| Source | My IP |

---

## 6️⃣ Acceso Final

URL de acceso:

```
http://TU_IP_PUBLICA_EC2:8080
```

Credenciales:
- Servidor: db
- Usuario: root
- Contraseña: definida en docker-compose.yml

---

## 7️⃣ Buenas Prácticas

- No exponer el puerto 3306 en producción
- Usar contraseñas seguras
- Utilizar archivos `.env`
- No usar root en producción
- Realizar backups periódicos
- Usar volúmenes Docker para persistencia

---

## 8️⃣ Próximos Pasos

- Agregar volúmenes persistentes
- Integrar backend (Node / Java)
- Configurar HTTPS
- Automatizar despliegue
- Migrar a RDS si escala
