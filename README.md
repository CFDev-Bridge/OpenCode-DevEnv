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
- Workspace compartido mediante **bind mount**.
- Proyectos de trabajo excluidos del control de versiones.
- Configuración del entorno versionada mediante Git.
- Posibilidad de reconstruir el entorno en otra computadora.

---

## 🏗️ Estructura del proyecto

```text
OpenCode-DevEnv/
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
| `docker/Dockerfile` | Define la imagen del entorno de desarrollo. |
| `docker-compose.yml` | Define y administra el contenedor OpenCode. |
| `workspace/` | Espacio de trabajo utilizado por OpenCode. |
| `.gitignore` | Evita versionar los proyectos y archivos de trabajo. |
| `README.md` | Documentación del entorno. |

---

# 💻 Requisitos

Antes de utilizar el entorno se necesita instalar:

- **Git**
- **Docker Desktop**
- Una cuenta de GitHub, si se desea utilizar el repositorio remoto.

Docker Desktop debe estar iniciado antes de ejecutar los comandos de Docker.

### GitHub mediante SSH

El repositorio utiliza una URL SSH:

```text
git@github.com:CFDev-Bridge/OpenCode-DevEnv.git
```

Por lo tanto, para clonar y utilizar el repositorio mediante SSH, es necesario tener configurada una clave SSH para GitHub.

---

# 🐳 Configuración de Docker Desktop

El entorno utiliza un **bind mount** entre una carpeta de Windows y el contenedor.

La relación entre ambas ubicaciones es:

```text
Windows
C:\ProyectosOpenCode\workspace
        │
        ▼
Docker
/workspace
```

Por esta razón, Docker Desktop debe tener acceso a la ubicación donde se encuentra el proyecto.

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

De esta manera, Docker podrá utilizar esta carpeta y sus subdirectorios para realizar el bind mount.

> **Nota:** Si el repositorio se clona en otra ubicación de Windows, esa nueva ubicación puede requerir autorización en Docker Desktop para utilizar bind mounts.

---

# 📥 Clonar el repositorio

Desde Git Bash:

```bash
cd C:\
```

Clonar el repositorio:

```bash
git clone git@github.com:CFDev-Bridge/OpenCode-DevEnv.git
```

Ingresar al proyecto:

```bash
cd C:\OpenCode-DevEnv
```

> Si se desea mantener específicamente la ubicación `C:\ProyectosOpenCode`, se puede clonar indicando el directorio de destino:
>
> ```bash
> git clone git@github.com:CFDev-Bridge/OpenCode-DevEnv.git C:\ProyectosOpenCode
> ```

---

# 🚀 Iniciar el entorno

Desde la carpeta del proyecto:

```bash
cd C:\ProyectosOpenCode
```

Levantar el entorno:

```bash
docker compose up -d
```

La primera vez, Docker Compose construirá la imagen definida en:

```text
docker/Dockerfile
```

Ingresar al contenedor:

```bash
docker exec -it opencode bash
```

Una vez dentro del contenedor, ejecutar:

```bash
opencode
```

El flujo completo es:

```text
Docker Desktop
      │
      ▼
docker compose up -d
      │
      ▼
Contenedor OpenCode
      │
      ▼
docker exec -it opencode bash
      │
      ▼
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

Los proyectos de trabajo se crean dentro de:

```text
/workspace
```

y se almacenan físicamente en:

```text
C:\ProyectosOpenCode\workspace
```

---

# 📂 Workspace

El directorio de trabajo dentro del contenedor es:

```text
/workspace
```

Este directorio está vinculado mediante un **bind mount** con:

```text
C:\ProyectosOpenCode\workspace
```

La relación es:

```text
Windows                              Contenedor
───────────────────────────────────────────────
C:\ProyectosOpenCode\
└── workspace\       <────────────>  /workspace
```

Por lo tanto, los archivos creados o modificados por OpenCode dentro de `/workspace` permanecen físicamente en la computadora anfitriona.

---

# 🔒 Control de versiones

El repositorio está diseñado para **versionar el entorno de OpenCode, no los proyectos de trabajo**.

El archivo `.gitignore` excluye todo el contenido de:

```text
/workspace/*
```

pero conserva:

```text
workspace/.gitkeep
```

Esto permite que Git mantenga la carpeta `workspace/` dentro de la estructura del repositorio aunque se encuentre vacía.

### Ejemplo

Si OpenCode crea:

```text
workspace/
├── proyecto1/
├── proyecto2/
└── proyecto3/
```

estos proyectos **no serán incluidos en Git**.

El repositorio mantiene únicamente los archivos necesarios para reconstruir el entorno:

```text
docker/
docker-compose.yml
.gitignore
README.md
workspace/.gitkeep
```

### ⚠️ Importante

Antes de realizar un commit, se recomienda revisar siempre:

```bash
git status
```

para verificar qué archivos serán incluidos en el control de versiones.

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

> Salir del contenedor no necesariamente significa detenerlo. El contenedor puede continuar ejecutándose en segundo plano.

---

# ⏸️ Detener temporalmente el entorno

Para detener el contenedor sin eliminarlo:

```bash
docker compose stop
```

Para iniciarlo nuevamente:

```bash
docker compose start
```

---

# ⏹️ Apagar el entorno

Para detener y eliminar los contenedores administrados por Docker Compose:

```bash
docker compose down
```

Los archivos almacenados en:

```text
C:\ProyectosOpenCode\workspace
```

**no se eliminan**, porque se encuentran físicamente en Windows y están vinculados mediante un bind mount.

---

# 🔁 Volver a iniciar el entorno

Después de utilizar:

```bash
docker compose down
```

el entorno puede iniciarse nuevamente con:

```bash
cd C:\ProyectosOpenCode
docker compose up -d
```

Después:

```bash
docker exec -it opencode bash
```

y:

```bash
opencode
```

---

# 🔧 Reconstruir el entorno

Si se modifica el `Dockerfile`, es necesario reconstruir la imagen.

Se puede hacer con:

```bash
docker compose build
```

y posteriormente:

```bash
docker compose up -d
```

O directamente:

```bash
docker compose up -d --build
```

---

# 🔄 Actualizar el entorno desde GitHub

Si existen cambios nuevos en el repositorio remoto:

```bash
git pull
```

Si se modificó el `Dockerfile` o `docker-compose.yml`, reconstruir el entorno:

```bash
docker compose up -d --build
```

> Si existen cambios locales sin commit, revisarlos con `git status` antes de ejecutar `git pull`.

---

# 🧩 Comandos útiles de Docker

### Ver contenedores ejecutándose

```bash
docker ps
```

### Ver todos los contenedores

```bash
docker ps -a
```

### Ver el estado del proyecto Compose

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

### Iniciar un entorno detenido

```bash
docker compose start
```

### Detener y eliminar los contenedores

```bash
docker compose down
```

---

# 🌿 Comandos básicos de Git

### Ver el estado del repositorio

```bash
git status
```

### Ver las ramas

```bash
git branch
```

### Cambiar de rama

```bash
git switch main
```

o:

```bash
git switch desarrollo
```

### Ver el historial resumido

```bash
git log --oneline --graph --all
```

### Descargar cambios de GitHub

```bash
git pull
```

### Subir commits a GitHub

```bash
git push
```

---

# 🔐 Aislamiento del entorno

OpenCode se ejecuta dentro de un contenedor independiente.

El entorno incluye:

- Ubuntu 24.04.
- Usuario `developer`.
- Node.js 22 LTS.
- Git.
- Python 3.
- OpenCode.

La configuración de la imagen está definida en:

```text
docker/Dockerfile
```

La configuración del servicio está definida en:

```text
docker-compose.yml
```

El objetivo es mantener separada la configuración del entorno respecto de los proyectos desarrollados dentro de `workspace/`.

---

# ⚠️ Docker no está instalado dentro del contenedor

Este proyecto **no instala Docker dentro del contenedor OpenCode**.

Docker Desktop permanece instalado en el **sistema anfitrión** y es responsable de administrar el contenedor.

Dentro del contenedor, OpenCode puede crear y modificar archivos dentro de:

```text
/workspace
```

Sin embargo, el comando:

```bash
docker
```

no forma parte de la imagen definida en `docker/Dockerfile`.

Por lo tanto, las operaciones de Docker se ejecutan desde Git Bash o desde otra terminal del sistema anfitrión.

### Ejemplo

Desde Windows:

```bash
docker ps
docker compose up -d
docker compose down
```

Mientras que dentro del contenedor se ejecutan herramientas como:

```bash
git
python3
node
npm
opencode
```

---

# 🔐 Archivos sensibles

El repositorio incluye reglas para evitar versionar archivos de configuración que puedan contener información sensible.

Entre ellas:

```text
.env
.env.*
```

No se deben almacenar directamente en Git:

- Contraseñas.
- Tokens.
- API keys.
- Claves privadas.
- Credenciales.
- Archivos `.env` con información sensible.

Antes de realizar un commit se recomienda revisar:

```bash
git status
```

y, cuando sea necesario, revisar los cambios con:

```bash
git diff
```

---

# 📌 Resumen rápido

## Primera configuración

```text
1. Instalar Git.
2. Instalar Docker Desktop.
3. Configurar File Sharing si es necesario.
4. Configurar SSH para GitHub.
5. Clonar el repositorio.
6. Levantar Docker Compose.
7. Entrar al contenedor.
8. Ejecutar OpenCode.
```

## Uso diario

```bash
cd C:\ProyectosOpenCode
docker compose up -d
docker exec -it opencode bash
opencode
```

## Salir

```text
Ctrl + C
exit
```

## Detener temporalmente

```bash
docker compose stop
```

## Iniciar nuevamente

```bash
docker compose start
```

## Apagar completamente

```bash
docker compose down
```

## Reconstruir

```bash
docker compose up -d --build
```

## Actualizar desde GitHub

```bash
git pull
```

---

# 🎯 Objetivo del proyecto

Mantener un **entorno OpenCode reproducible, aislado y versionado mediante Git**, permitiendo reconstruir la configuración del entorno en otra computadora sin necesidad de realizar nuevamente toda la configuración manual.

Los proyectos de trabajo permanecen fuera del control de versiones dentro de:

```text
workspace/
```

De esta manera, el repositorio contiene la **infraestructura y configuración del entorno**, mientras que los proyectos desarrollados con OpenCode permanecen separados.
