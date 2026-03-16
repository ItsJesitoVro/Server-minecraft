# 🎮 Server Minecraft - Tilin Server

[![CI](https://github.com/ItsJesitoVro/Server-minecraft/actions/workflows/ci.yml/badge.svg)](https://github.com/ItsJesitoVro/Server-minecraft/actions/workflows/ci.yml)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/compose/)
[![Minecraft](https://img.shields.io/badge/Minecraft-Paper-62B47A?logo=modrinth&logoColor=white)](https://papermc.io/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Servidor de Minecraft dockerizado usando [itzg/minecraft-server](https://github.com/itzg/docker-minecraft-server), configurado con **Paper** para máximo rendimiento.

---

## 📋 Tabla de contenidos

- [Requisitos](#-requisitos)
- [Inicio rápido](#-inicio-rápido)
- [Configuración](#%EF%B8%8F-configuración)
- [Comandos útiles](#-comandos-útiles)
- [Arquitectura](#-arquitectura)
- [Mantenimiento](#-mantenimiento)
- [Contribuir](#-contribuir)

---

## 📦 Requisitos

| Herramienta | Versión mínima |
|-------------|----------------|
| [Docker](https://docs.docker.com/get-docker/) | 20.10+ |
| [Docker Compose](https://docs.docker.com/compose/install/) | 2.0+ |

> **Nota:** El servidor necesita al menos **3 GB de RAM** disponible en el host.

---

## 🚀 Inicio rápido

```bash
# 1. Clonar el repositorio
git clone git@github.com:ItsJesitoVro/Server-minecraft.git
cd Server-minecraft

# 2. Levantar el servidor
docker compose up -d

# 3. Ver los logs en tiempo real
docker compose logs -f minecraft
```

Una vez que veas `Done! For help, type "help"` en los logs, el servidor está listo.

### Conectarse al servidor

Abre Minecraft y conéctate a:

```
localhost:25565
```

> Si te conectas desde otra máquina en la misma red, usa la IP del host en lugar de `localhost`.

---

## ⚙️ Configuración

La configuración del servidor se maneja a través de variables de entorno en `docker-compose.yml`:

### General

| Variable | Valor actual | Descripción |
|----------|-------------|-------------|
| `TYPE` | `PAPER` | Motor del servidor (VANILLA, PAPER, SPIGOT, FABRIC, FORGE) |
| `VERSION` | `LATEST` | Versión de Minecraft |
| `MEMORY` | `2G` | RAM asignada a la JVM |
| `TZ` | `America/Mexico_City` | Zona horaria del contenedor |

### Gameplay

| Variable | Valor actual | Descripción |
|----------|-------------|-------------|
| `DIFFICULTY` | `normal` | Dificultad (peaceful, easy, normal, hard) |
| `MODE` | `survival` | Modo de juego (survival, creative, adventure, spectator) |
| `MAX_PLAYERS` | `5` | Jugadores máximos simultáneos |
| `ONLINE_MODE` | `false` | `false` = no requiere cuenta de Mojang |
| `ALLOW_NETHER` | `true` | Habilitar el Nether |
| `ALLOW_FLIGHT` | `false` | Permitir volar en survival |
| `VIEW_DISTANCE` | `10` | Distancia de renderizado (chunks) |
| `MAX_WORLD_SIZE` | `10000` | Tamaño máximo del mundo (bloques) |
| `SPAWN_PROTECTION` | `16` | Radio de protección del spawn |

### Mundo

| Variable | Valor actual | Descripción |
|----------|-------------|-------------|
| `LEVEL_TYPE` | `DEFAULT` | Tipo de mundo (DEFAULT, FLAT, LARGEBIOMES, AMPLIFIED) |
| `SEED` | *(vacío)* | Semilla del mundo (vacío = aleatoria) |
| `MOTD` | `Tilin Server - ¡Bienvenidos a la aventura!` | Mensaje del día |

### Recursos del contenedor

| Recurso | Valor |
|---------|-------|
| Memoria límite | 3 GB |
| Memoria reservada | 2 GB |

Para modificar cualquier valor, edita `docker-compose.yml` y reinicia:

```bash
docker compose down && docker compose up -d
```

---

## 💻 Comandos útiles

### Docker Compose

```bash
# Levantar el servidor (en background)
docker compose up -d

# Detener el servidor
docker compose down

# Reiniciar el servidor
docker compose restart minecraft

# Ver logs en tiempo real
docker compose logs -f minecraft

# Ver el estado del contenedor
docker compose ps
```

### Consola del servidor

```bash
# Abrir la consola interactiva de Minecraft
docker attach minecraft-server

# Para salir de la consola sin detener el servidor: Ctrl+P, Ctrl+Q
```

```bash
# Ejecutar un comando sin entrar a la consola
docker exec minecraft-server rcon-cli <comando>

# Ejemplos:
docker exec minecraft-server rcon-cli list          # Ver jugadores conectados
docker exec minecraft-server rcon-cli op <jugador>  # Dar OP a un jugador
docker exec minecraft-server rcon-cli whitelist add <jugador>  # Agregar a whitelist
docker exec minecraft-server rcon-cli say "¡Hola a todos!"    # Mensaje global
```

### Backups

```bash
# Crear un backup del volumen de datos
docker run --rm -v minecraft_data:/data -v $(pwd)/backups:/backup \
  busybox tar czf /backup/minecraft-backup-$(date +%Y%m%d-%H%M%S).tar.gz /data

# Restaurar un backup
docker compose down
docker run --rm -v minecraft_data:/data -v $(pwd)/backups:/backup \
  busybox sh -c "rm -rf /data/* && tar xzf /backup/<nombre-del-backup>.tar.gz -C /"
docker compose up -d
```

---

## 🏗 Arquitectura

```
Server-minecraft/
├── docker-compose.yml    # Definición del servicio y configuración
├── README.md             # Este archivo
└── .gitignore            # Archivos ignorados por git

Volumen Docker:
└── minecraft_data        # Datos persistentes (mundo, plugins, configs)
```

### Persistencia

Los datos del servidor se almacenan en el volumen Docker `minecraft_data`. Esto incluye:

- 🌍 **Mundos** (world, world_nether, world_the_end)
- 🔌 **Plugins** y sus configuraciones
- 📄 **Configuración** del servidor (server.properties, etc.)
- 🚫 **Bans, whitelist, ops**

Los datos persisten aunque el contenedor se elimine. Solo se pierden si se borra el volumen explícitamente.

### Health Check

El contenedor incluye un health check que verifica si el servidor está respondiendo:

- **Intervalo:** cada 30 segundos
- **Timeout:** 10 segundos
- **Inicio:** espera 60 segundos antes de empezar a verificar
- **Reintentos:** 5 intentos antes de marcar como unhealthy

---

## 🔧 Mantenimiento

### Actualizar el servidor

```bash
# Jalar la última imagen y recrear el contenedor
docker compose pull
docker compose up -d
```

### Ver el uso de recursos

```bash
docker stats minecraft-server
```

### Limpiar todo (⚠️ DESTRUCTIVO)

```bash
# Eliminar contenedor Y datos del mundo
docker compose down -v
```

> **Advertencia:** El flag `-v` elimina el volumen con todos los datos del mundo. Haz un backup antes.

---

## 🤝 Contribuir

1. Crea una branch desde `main`:
   ```bash
   git checkout -b feature/mi-cambio
   ```

2. Haz tus cambios y commitea (**los commits deben estar firmados con GPG**):
   ```bash
   git commit -S -m "feat: descripción del cambio"
   ```

3. Push a tu branch:
   ```bash
   git push origin feature/mi-cambio
   ```

4. Abre un **Pull Request** hacia `main` en GitHub.

> **Reglas del repositorio:**
> - No se permite push directo a `main`
> - Todo merge requiere un PR aprobado por el owner
> - Todos los commits deben estar firmados (GPG verified ✅)

---

<div align="center">

**Tilin Server** 🐸 — *¡Bienvenidos a la aventura!*

</div>