# Entorno OpenCode con Docker

Entorno de desarrollo aislado para ejecutar **OpenCode** mediante **Docker**, manteniendo la configuración del entorno separada de los proyectos de trabajo.

El repositorio contiene únicamente la configuración necesaria para reconstruir el entorno. Los proyectos y archivos de trabajo almacenados dentro de `workspace/` **no forman parte del repositorio Git**.

---

## 📋 Características

- Ubuntu 24.04 como sistema base.
- Node.js 22 LTS.
- OpenCode instalado dentro del contenedor.
- Git disponible dentro del entorno.
- Python 3 disponible.
- Docker Compose para administrar el entorno.
- OpenCode ejecutándose con el usuario `developer`.
- Workspace persistente mediante bind mount.
- Proyectos de trabajo excluidos del control de versiones.

---

## 🏗️ Estructura del proyecto

```text
ProyectosOpenCode/
│
├── docker/
│   └── Dockerfile
│
├── workspace/
│   └── .gitkeep
│
├── docker-compose.yml
├── .gitignore
└── README.md
```

### Descripción

| Archivo / carpeta | Descripción |
|---|---|
| `docker/Dockerfile` | Define la imagen del entorno OpenCode. |
| `docker-compose.yml` | Define y administra el contenedor. |
| `workspace/` | Espacio de trabajo utilizado por OpenCode. |
| `.gitignore` | Evita subir los proyectos de trabajo al repositorio. |
| `README.md` | Documentación del entorno. |

---

# 💻 Requisitos

Antes de utilizar el entorno se necesita instalar:

- **Git**
- **Docker Desktop**
- Una cuenta de GitHub, si se desea trabajar con el repositorio remoto.

Docker Desktop debe estar iniciado antes de ejecutar los comandos de Docker.

---

# 🐳 Configuración de Docker Desktop

El entorno utiliza un **bind mount** entre una carpeta de Windows y el contenedor.

La configuración utilizada es:

```text
Windows
C:\ProyectosOpenCode\workspace
        │
        ▼
Docker
/workspace
```

Por esta razón, Docker Desktop debe tener autorizada la carpeta:

```text
C:\ProyectosOpenCode
```

### Configurar File Sharing

En Docker Desktop:

```text
Settings
   ↓
Resources
   ↓
File Sharing
```

Verificar que aparezca:

```text
C:\ProyectosOpenCode
```

Docker podrá utilizar esta carpeta y sus subdirectorios para realizar bind mounts.

> **Nota:** Si el repositorio se clona en una ubicación diferente, esa nueva ubicación deberá estar autorizada en Docker Desktop cuando sea necesario realizar bind mounts desde Windows.

---

# 📥 Clonar el proyecto

Desde Git Bash:

```bash
cd C:\
```

Clonar el repositorio:

```bash
git clone git@github.com:CFDev-Bridge/ProyectosOpenCode.git
```

Ingresar al proyecto:

```bash
cd C:\ProyectosOpenCode
```

---

# 🚀 Iniciar el entorno

Una vez instalado Docker Desktop y configurado File Sharing, ejecutar:

```bash
cd C:\ProyectosOpenCode
```

Levantar el entorno:

```bash
docker compose up -d
```

> La primera vez, Docker Compose puede construir la imagen definida en `docker/Dockerfile`.

Ingresar al contenedor:

```bash
docker exec -it opencode bash
```

Iniciar OpenCode:

```bash
opencode
```

---

# 🔄 Flujo de trabajo habitual

Cada vez que se quiera utilizar OpenCode:

```bash
cd C:\ProyectosOpenCode
docker compose up -d
docker exec -it opencode bash
opencode
```

El flujo es:

```text
Docker Desktop
      ↓
docker compose up -d
      ↓
Contenedor OpenCode
      ↓
docker exec -it opencode bash
      ↓
opencode
```

---

# 📂 Workspace

Dentro del contenedor, OpenCode trabaja en:

```text
/workspace
```

Esta carpeta corresponde a:

```text
C:\ProyectosOpenCode\workspace
```

en Windows.

Por lo tanto:

```text
Windows                         Contenedor
────────────────────────────────────────────
C:\ProyectosOpenCode\
└── workspace\       <───────>  /workspace
```

Los archivos creados o modificados dentro de `/workspace` permanecen físicamente en la carpeta `workspace` de Windows.

---

# 🔒 Control de versiones

El repositorio está diseñado para **versionar el entorno de OpenCode, no los proyectos de trabajo**.

El archivo `.gitignore` excluye:

```text
/workspace/*
```

pero mantiene:

```text
workspace/.gitkeep
```

Esto permite que Git conserve la carpeta `workspace/` aunque se encuentre vacía.

### Ejemplo

Si OpenCode crea:

```text
workspace/
├── proyecto1/
├── proyecto2/
└── proyecto3/
```

estos proyectos **no serán incluidos en Git**.

El repositorio mantiene únicamente los archivos necesarios para reconstruir el entorno, por ejemplo:

```text
docker/
docker-compose.yml
.gitignore
README.md
workspace/.gitkeep
```

---

# 🛑 Salir de OpenCode

Para salir de OpenCode:

```text
Ctrl + C
```

Después, para salir del contenedor:

```bash
exit
```

Esto devuelve la terminal al sistema anfitrión.

> Salir del contenedor no significa necesariamente detenerlo. El contenedor puede continuar ejecutándose en segundo plano.

---

# ⏸️ Detener el entorno temporalmente

Si se desea detener los contenedores sin eliminarlos:

```bash
docker compose stop
```

Posteriormente pueden iniciarse nuevamente con:

```bash
docker compose start
```

---

# ⏹️ Apagar completamente el entorno

Si se desea detener y eliminar los contenedores creados por Docker Compose:

```bash
docker compose down
```

Los archivos del `workspace` **no se eliminan**, ya que se encuentran almacenados físicamente en Windows.

---

# 🔁 Volver a iniciar después de apagarlo

Cuando se necesite utilizar nuevamente el entorno:

```bash
cd C:\ProyectosOpenCode
docker compose up -d
docker exec -it opencode bash
opencode
```

---

# 🔧 Reconstruir el entorno

Si se modifica el `Dockerfile` y es necesario reconstruir la imagen:

```bash
docker compose build
```

Luego:

```bash
docker compose up -d
```

También se puede realizar directamente:

```bash
docker compose up -d --build
```

---

# 🔄 Actualizar el entorno desde Git

Si el repositorio tiene cambios nuevos en GitHub:

```bash
git pull
```

Después, si se modificó el `Dockerfile` o `docker-compose.yml`, reconstruir el entorno:

```bash
docker compose up -d --build
```

---

# 🧩 Comandos útiles

### Ver contenedores ejecutándose

```bash
docker ps
```

### Ver todos los contenedores

```bash
docker ps -a
```

### Ver el estado del entorno

```bash
docker compose ps
```

### Ver los registros del contenedor

```bash
docker compose logs
```

### Ver los registros en tiempo real

```bash
docker compose logs -f
```

### Reiniciar el entorno

```bash
docker compose restart
```

### Detener el entorno

```bash
docker compose stop
```

### Iniciar nuevamente un entorno detenido

```bash
docker compose start
```

### Apagar y eliminar los contenedores

```bash
docker compose down
```

---

# 🔐 Aislamiento del entorno

OpenCode se ejecuta dentro de un contenedor independiente.

El contenedor utiliza:

- Ubuntu 24.04.
- Usuario `developer`.
- Node.js 22 LTS.
- Git.
- Python 3.
- OpenCode.

La configuración del contenedor está definida en:

```text
docker/Dockerfile
```

La administración del servicio está definida en:

```text
docker-compose.yml
```

El objetivo es poder reconstruir el entorno en otra computadora sin tener que realizar nuevamente toda la configuración manual de OpenCode.

---

# ⚠️ Importante

Este proyecto **no instala Docker dentro del contenedor OpenCode**.

Docker Desktop permanece instalado en el sistema anfitrión y es quien administra el contenedor.

OpenCode puede crear y modificar archivos del proyecto dentro de:

```text
/workspace
```

pero el comando:

```bash
docker
```

no forma parte del entorno interno de OpenCode.

Por lo tanto, las operaciones de Docker se ejecutan normalmente desde la terminal del sistema anfitrión.

---

# 📌 Resumen rápido

### Primera configuración

```text
1. Instalar Git.
2. Instalar Docker Desktop.
3. Agregar C:\ProyectosOpenCode a File Sharing.
4. Clonar el repositorio.
5. Levantar Docker Compose.
6. Entrar al contenedor.
7. Ejecutar OpenCode.
```

### Uso diario

```bash
cd C:\ProyectosOpenCode
docker compose up -d
docker exec -it opencode bash
opencode
```

### Salir

```text
Ctrl + C
exit
```

### Detener temporalmente

```bash
docker compose stop
```

### Apagar completamente

```bash
docker compose down
```

---

## 🎯 Objetivo del proyecto

Mantener un **entorno OpenCode reproducible, aislado y versionado mediante Git**, permitiendo reconstruir la configuración del entorno en otra computadora sin versionar los proyectos de trabajo desarrollados dentro de `workspace/`.