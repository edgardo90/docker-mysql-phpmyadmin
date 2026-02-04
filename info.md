# 📘 Guía Completa de Configuración  
## Docker, MySQL y phpMyAdmin en AWS EC2

---

## 📌 Información General

### Entorno
- Amazon Linux 2023  
- Amazon Linux 2  
- Instancia EC2 (AWS)

### Objetivo

Desplegar de forma **automatizada**, **controlada** y **reproducible** una base de datos **MySQL** junto con una interfaz web **phpMyAdmin**, utilizando **Docker** y **Docker Compose** sobre una instancia EC2.

El objetivo principal es **evitar instalaciones manuales**, aislar los servicios en contenedores y facilitar el mantenimiento, despliegue y replicación del entorno.

---

## 🧠 Conceptos Básicos (Teoría)

### ¿Qué es Docker?

Docker es una plataforma de contenedores que permite empaquetar aplicaciones y sus dependencias en unidades aisladas llamadas **contenedores**.  
Cada contenedor:
- Es independiente
- Usa el mismo kernel del sistema
- Se inicia y destruye rápidamente
- No afecta al sistema operativo base

En este caso:
- Un contenedor ejecuta **MySQL**
- Otro contenedor ejecuta **phpMyAdmin**

---

### ¿Qué es Docker Compose?

Docker Compose es una herramienta que permite **definir y ejecutar múltiples contenedores** usando un único archivo (`docker-compose.yml`).

Beneficios:
- Orquestación simple
- Configuración centralizada
- Reproducibilidad
- Arranque de todo el stack con un solo comando

---

### ¿Qué es phpMyAdmin?

phpMyAdmin es una **interfaz web** para administrar bases de datos MySQL:
- Crear y borrar bases de datos
- Ejecutar consultas SQL
- Administrar usuarios
- Ver tablas y registros

---

## 1️⃣ Instalación y Configuración de Docker

En este paso se prepara el sistema para poder ejecutar contenedores.

---

### 1.1 Actualizar el sistema operativo

```bash
sudo dnf update -y
```

Este comando:
- Actualiza todos los paquetes instalados
- Reduce errores de compatibilidad
- Es recomendable ejecutarlo siempre antes de instalar software nuevo

---

### 1.2 Instalar Docker

```bash
sudo dnf install docker -y
```

Instala:
- Docker Engine
- Dependencias necesarias para ejecutar contenedores

---

### 1.3 Iniciar el servicio Docker

```bash
sudo systemctl start docker
```

Este comando:
- Enciende el motor Docker
- Permite comenzar a ejecutar contenedores

---

### 1.4 Habilitar Docker al iniciar el sistema

```bash
sudo systemctl enable docker
```

Hace que Docker:
- Se inicie automáticamente
- No requiera intervención manual tras reinicios de la EC2

---

### 1.5 Agregar el usuario al grupo Docker

```bash
sudo usermod -aG docker ec2-user
```

Esto permite:
- Ejecutar comandos Docker sin `sudo`
- Evitar problemas de permisos

---

### 1.6 Aplicar los permisos

```bash
exit
```

Cerrar la sesión SSH y volver a conectarse para que el cambio de grupo tenga efecto.

---

## 2️⃣ Instalación de Docker Compose

Docker Compose permite administrar múltiples contenedores con un solo archivo.

---

### 2.1 Instalar Docker Compose Plugin

```bash
sudo dnf install docker-compose-plugin -y
```
si da error
```bash
sudo mkdir -p /usr/local/lib/docker/cli-plugins
```
```bash
sudo curl -SL https://github.com/docker/compose/releases/download/v2.25.0/docker-compose-linux-x86_64 \-o /usr/local/lib/docker/cli-plugins/docker-compose
```
```bash
sudo chmod +x /usr/local/lib/docker/cli-plugins/docker-compose
```

Instala el plugin oficial compatible con Docker moderno.

---

### 2.2 Verificar la instalación

```bash
docker compose version
```

Si se muestra la versión, Docker Compose está listo para usar.

---

## 3️⃣ Creación del Entorno de Base de Datos

Aquí se define la estructura del proyecto y la orquestación de servicios.

---

### 3.1 Crear carpeta del proyecto

```bash
mkdir servidor-datos
cd servidor-datos
```

Esta carpeta contendrá:
- El archivo `docker-compose.yml`
- Configuraciones futuras
- Volúmenes o scripts si se agregan luego

---

### 3.2 Crear archivo docker-compose.yml

```bash
nano docker-compose.yml
```

---

### 3.3 Contenido completo del archivo

```yaml
version: '3.8'

services:
  db:
    image: mysql:8.0
    container_name: mysql_db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root1234
      MYSQL_DATABASE: mi_base_datos
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: pma_web
    restart: always
    depends_on:
      - db
    ports:
# PUERTO_DE_TU_MAQUINA(donde lo voy abrir en mi navegador) : PUERTO_DENTRO_DEL_CONTENEDOR
      - "8080:80"
    environment:
      PMA_HOST: db

volumes:
  mysql_data:

```

---

### 3.4 Explicación del archivo docker-compose.yml

#### Servicio `db` (MySQL)

- `image`: usa la imagen oficial de MySQL 8
- `container_name`: nombre identificable del contenedor
- `restart: always`: se reinicia automáticamente si falla
- `environment`:
  - Define la contraseña root
  - Crea una base de datos inicial
- `ports`: expone el puerto 3306

---

#### Servicio `phpmyadmin`

- Usa la imagen oficial de phpMyAdmin
- `depends_on`: espera a que MySQL esté disponible
- Expone el puerto 8080
- Se conecta al servicio `db` internamente

---

## 4️⃣ Despliegue y Comandos Útiles

---

### 4.1 Levantar todo el entorno

```bash
docker compose up -d
```

- Crea red interna
- Descarga imágenes
- Inicia contenedores en segundo plano

---

### 4.2 Ver contenedores activos

```bash
docker ps
```

---

### 4.3 Ver logs (diagnóstico)

```bash
docker compose logs -f
```

Útil para:
- Detectar errores
- Ver estado de MySQL
- Ver problemas de conexión

---

### 4.4 Detener y eliminar contenedores

```bash
docker compose down
```

No borra imágenes, solo contenedores y red.

---

## 5️⃣ Configuración de Red en AWS (Security Group)

Para acceder a phpMyAdmin desde el navegador es necesario abrir el puerto.

### Pasos en AWS Console

1. EC2 → Instances  
2. Seleccionar la instancia  
3. Pestaña **Security**  
4. Click en **Security Group**  
5. Edit inbound rules  
6. Agregar regla:

| Campo | Valor |
|------|------|
| Type | Custom TCP |
| Port Range | 8080 |
| Source | My IP |

Guardar los cambios.

---

## 6️⃣ Acceso Final

Desde el navegador web:

```
http://TU_IP_PUBLICA_EC2:8080
```

### Credenciales

- Servidor: `db`
- Usuario: `root`
- Contraseña: la definida en el archivo docker-compose.yml

---

## 📌 Conclusión

Este enfoque permite:
- Desplegar bases de datos rápidamente
- Evitar instalaciones locales complejas
- Mantener entornos limpios y controlados
- Escalar o replicar fácilmente el stack

Este documento puede utilizarse como:
- Manual técnico
- README.md
- Base para documentación profesional
