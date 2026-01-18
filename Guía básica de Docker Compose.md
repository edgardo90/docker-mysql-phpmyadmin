# 📦 Guía básica de Docker Compose

Esta guía explica **qué pasa realmente cuando usás Docker Compose**, por qué **no se reinstala todo cada vez**, y cómo funcionan `start`, `stop`, `up` y `down`.

La idea es que la puedas guardar y volver a leer en el futuro sin miedo a romper nada.

---

## 🧠 Idea clave (para grabar en la cabeza)

> **Docker NO reinstala nada si ya existe.**
> Solo **arranca o detiene contenedores que ya fueron creados**.

---

## 🧱 Conceptos fundamentales (teoría básica)

Antes de los comandos, entendamos las piezas.

### 1️⃣ Imagen

* Es como una **plantilla** o **instalador**
* Ejemplos:

  * `mysql:8.0`
  * `phpmyadmin/phpmyadmin`
* Contiene:

  * sistema base
  * programa instalado
  * configuración por defecto

📌 Una imagen **no se ejecuta sola**.

---

### 2️⃣ Contenedor

* Es una **instancia en ejecución** de una imagen
* Tiene:

  * un ID
  * un nombre (`mysql_db`, `pma_web`)
  * estado (running / stopped)

📌 Un contenedor **sí se puede apagar y prender**.

---

### 3️⃣ Volumen

* Es el **disco duro** de Docker
* Vive **fuera del contenedor**
* Se usa para datos persistentes (bases de datos)

Ejemplo:

```yaml
volumes:
  - mysql_data:/var/lib/mysql
```

📌 Si el contenedor muere, **el volumen sigue existiendo**.

---

### 4️⃣ Docker Compose

* Es un **orquestador**
* Lee un archivo `docker-compose.yml`
* Define:

  * servicios
  * puertos
  * redes
  * volúmenes

📌 Docker Compose trabaja por **proyecto**, y el proyecto es la **carpeta**.

---

## 📁 Importante: la carpeta del proyecto

Docker Compose **siempre usa el `.yml` del directorio actual**.

Por eso:

```bash
cd servidor-datos
```

Y luego:

```bash
docker compose start
```

Si no estás en esa carpeta, Docker **no sabe qué contenedores manejar**.

---

## ▶️ Comandos principales (explicados)

### 🟡 `docker compose up`

```bash
docker compose up
```

Qué hace:

* crea contenedores **si no existen**
* crea redes y volúmenes
* descarga imágenes si faltan
* arranca todo

📌 Si ya existe, **NO lo reinstala**, solo lo arranca.

---

### 🟡 `docker compose up -d`

```bash
docker compose up -d
```

Igual que `up`, pero:

* corre en segundo plano (*detached*)
* no bloquea la terminal

---

### 🛑 `docker compose stop`

```bash
docker compose stop
```

Qué hace:

* apaga los contenedores
* mantiene:

  * imágenes
  * volúmenes
  * configuración

📌 **NO borra nada**.

---

### ▶️ `docker compose start`

```bash
docker compose start
```

Qué hace:

* prende **los mismos contenedores** que ya existían
* conserva:

  * IDs
  * datos
  * puertos

📌 No crea contenedores nuevos.

---

### ❌ `docker compose down`

```bash
docker compose down
```

Qué hace:

* apaga contenedores
* los elimina
* elimina la red

📌 **NO borra volúmenes por defecto**.

---

### 💥 `docker compose down -v`

```bash
docker compose down -v
```

Qué hace:

* borra contenedores
* borra volúmenes
* borra datos

⚠️ **Peligroso para bases de datos**.

---

## 🧪 Cómo comprobar que NO se reinstala nada

1️⃣ Ver IDs:

```bash
docker ps -a
```

2️⃣ Parar:

```bash
docker compose stop
```

3️⃣ Arrancar:

```bash
docker compose start
```

4️⃣ Ver IDs otra vez:

```bash
docker ps
```

👉 Si el ID es el mismo, **es el mismo contenedor**.

---

## 💾 Por qué los datos no se pierden (MySQL)

Porque usás un volumen:

```yaml
volumes:
  - mysql_data:/var/lib/mysql
```

Eso significa:

* MySQL guarda los datos en `mysql_data`
* El volumen vive aunque el contenedor se apague

📌 Solo se pierde si:

```bash
docker volume rm mysql_data
```

o

```bash
docker compose down -v
```

---

## 🧠 Resumen mental rápido

Pensalo así:

* 🧱 Imagen = instalador
* 📦 Contenedor = programa corriendo
* 💾 Volumen = disco

| Comando | Qué hace          |
| ------- | ----------------- |
| stop    | apaga             |
| start   | prende            |
| up      | crea si no existe |
| down    | borra             |

---

## 🎯 Conclusión

* Docker **recuerda todo**
* `start` **no reinstala nada**
* los datos siguen existiendo
* solo vos podés borrarlos explícitamente

Esta es la base para trabajar **sin miedo** con Docker.

