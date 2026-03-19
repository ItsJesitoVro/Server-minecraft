# 🎮 Server Minecraft - Tilin Server

[![CI](https://github.com/ItsJesitoVro/Server-minecraft/actions/workflows/ci.yml/badge.svg)](https://github.com/ItsJesitoVro/Server-minecraft/actions/workflows/ci.yml)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker&logoColor=white)](https://docs.docker.com/compose/)
[![Minecraft](https://img.shields.io/badge/Minecraft-1.21.4-62B47A?logo=modrinth&logoColor=white)](https://www.minecraft.net/)
[![Paper](https://img.shields.io/badge/Paper-Server-EEE?logo=paperlessngx&logoColor=black)](https://papermc.io/)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Servidor de Minecraft dockerizado usando [itzg/minecraft-server](https://github.com/itzg/docker-minecraft-server), configurado con **Paper** y plugins preinstalados. Los jugadores se conectan con un **Minecraft vanilla** — no necesitan instalar nada extra.

---

## 📋 Tabla de contenidos

- [Requisitos](#-requisitos)
- [Inicio rápido](#-inicio-rápido)
- [Plugins incluidos](#-plugins-incluidos)
- [Configuración del servidor](#%EF%B8%8F-configuración-del-servidor)
- [Comandos útiles](#-comandos-útiles)
- [Arquitectura del proyecto](#-arquitectura-del-proyecto)
- [Mantenimiento](#-mantenimiento)
- [Contribuir](#-contribuir)

---

## 📦 Requisitos

### Para el administrador del servidor

| Herramienta | Versión mínima |
|-------------|----------------|
| [Docker](https://docs.docker.com/get-docker/) | 20.10+ |
| [Docker Compose](https://docs.docker.com/compose/install/) | 2.0+ |

> **Nota:** El servidor necesita al menos **3 GB de RAM** disponible en el host.

### Para los jugadores

| Requisito | Detalle |
|-----------|---------|
| Minecraft | **Java Edition 1.21.4** |
| Mods/Plugins | **Ninguno** — los plugins son 100% server-side |
| Cuenta premium | **No requerida** (ONLINE_MODE: false) |

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

El servidor descargará automáticamente **Paper** y los **plugins** en el primer arranque. Espera a ver `Done! For help, type "help"` en los logs.

### Conectarse al servidor

Abre Minecraft **1.21.4** (vanilla) y conéctate a:

```
<IP-del-host>:25565
```

> 🟢 No necesitas instalar mods, loaders ni nada extra. Los plugins corren en el servidor.

---

## 🔌 Plugins incluidos

Los plugins se descargan automáticamente al iniciar el servidor.

| Plugin | Fuente | Descripción |
|--------|--------|-------------|
| **EssentialsX** | [Modrinth](https://modrinth.com/plugin/essentialsx) | Comandos esenciales: `/home`, `/tpa`, `/spawn`, `/warp`, kits, economía básica y más |
| **Vault** | [GitHub Releases](https://github.com/MilkBowl/Vault/releases) | API de permisos y economía — puente entre plugins |
| **LuckPerms** | [Modrinth](https://modrinth.com/plugin/luckperms) | Sistema avanzado de permisos y rangos con editor web |

### Agregar plugins personalizados

Para agregar plugins que no están en Modrinth ni en GitHub Releases:

1. Descarga el `.jar` del plugin (compatible con **Paper 1.21.4**)
2. Colócalo en la carpeta `plugins/`
3. Reinicia el servidor: `docker compose restart minecraft`

> 📖 Más detalles en [`plugins/README.md`](plugins/README.md)

---

## ⚙️ Configuración del servidor

La configuración se maneja a través de variables de entorno en `docker-compose.yml`:

### General

| Variable | Valor actual | Descripción |
|----------|-------------|-------------|
| `TYPE` | `PAPER` | Motor del servidor (optimizado para plugins) |
| `VERSION` | `1.21.4` | Versión de Minecraft (fijada para compatibilidad con plugins) |
| `MEMORY` | `2G` | RAM asignada a la JVM |
| `TZ` | `America/Mexico_City` | Zona horaria del contenedor |

### Plugins automáticos

| Variable | Valor actual | Descripción |
|----------|-------------|-------------|
| `MODRINTH_PROJECTS` | `essentialsx,luckperms` | Slugs de plugins descargados desde Modrinth |
| `PLUGINS` | `https://github.com/MilkBowl/Vault/releases/download/1.7.3/Vault.jar` | URLs directas de plugins (GitHub Releases, etc.) |
| `COPY_PLUGINS_DEST` | `/data/plugins` | Destino donde se copian los plugins del directorio `plugins/` |

### Gameplay

| Variable | Valor actual | Descripción |
|----------|-------------|-------------|
| `DIFFICULTY` | `normal` | Dificultad (peaceful, easy, normal, hard) |
| `MODE` | `survival` | Modo de juego (survival, creative, adventure, spectator) |
| `MAX_PLAYERS` | `5` | Jugadores máximos simultáneos |
| `ONLINE_MODE` | `false` | `false` = no requiere cuenta premium de Mojang |
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
docker exec minecraft-server rcon-cli list                     # Ver jugadores conectados
docker exec minecraft-server rcon-cli op <jugador>             # Dar OP a un jugador
docker exec minecraft-server rcon-cli whitelist add <jugador>  # Agregar a whitelist
docker exec minecraft-server rcon-cli say "¡Hola a todos!"    # Mensaje global
```

### Comandos de plugins (en la consola del servidor o con rcon-cli)

```bash
# EssentialsX
docker exec minecraft-server rcon-cli ess reload              # Recargar configuración

# LuckPerms
docker exec minecraft-server rcon-cli lp user <jugador> info  # Ver info de un jugador
docker exec minecraft-server rcon-cli lp editor                # Abrir editor web de permisos
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

## 🏗 Arquitectura del proyecto

```
Server-minecraft/
├── .github/
│   └── workflows/
│       └── ci.yml              # CI: valida compose + health check del server
├── plugins/
│   ├── README.md               # Instrucciones para plugins personalizados
│   └── .gitkeep                # Mantiene el directorio en git
├── docker-compose.yml          # Definición del servicio y configuración
├── README.md                   # Este archivo
└── .gitignore                  # Archivos ignorados por git

Volumen Docker:
└── minecraft_data              # Datos persistentes (mundo, plugins, configs)
```

### Flujo de plugins

```
                        ┌─────────────────────┐
                        │  docker compose up   │
                        └─────────┬───────────┘
                                  │
              ┌───────────────────┼───────────────────┐
              │                   │                     │
   ┌──────────▼──────────┐ ┌─────▼──────────┐ ┌───────▼────────────┐
   │ Modrinth (auto)     │ │ URL directa    │ │ plugins/ (manual)  │
   │                     │ │                │ │                    │
   │  EssentialsX        │ │  Vault         │ │  custom-plugin.jar │
   │  LuckPerms          │ │                │ │  otro-plugin.jar   │
   └──────────┬──────────┘ └─────┬──────────┘ └───────┬────────────┘
              │                   │                     │
              └───────────────────┼─────────────────────┘
                                  │
                       ┌──────────▼──────────┐
                       │  /data/plugins/ en  │
                       │  el contenedor      │
                       └─────────────────────┘
```

### Persistencia

Los datos del servidor se almacenan en el volumen Docker `minecraft_data`:

- 🌍 **Mundos** (world, world_nether, world_the_end)
- 🔌 **Plugins** descargados y sus configuraciones
- 📄 **Configuración** del servidor (server.properties, etc.)
- 🚫 **Bans, whitelist, ops**

Los datos persisten aunque el contenedor se elimine. Solo se pierden si se borra el volumen explícitamente.

### Health Check

El contenedor incluye un health check automático:

- **Intervalo:** cada 30 segundos
- **Timeout:** 10 segundos
- **Inicio:** espera 60 segundos antes de empezar a verificar
- **Reintentos:** 5 intentos antes de marcar como unhealthy

### CI/CD

El workflow de GitHub Actions (`.github/workflows/ci.yml`) se ejecuta en cada PR y push a `main`:

1. **Validate Docker Compose** — verifica que la sintaxis del compose sea válida
2. **Server Health Check** — levanta el servidor completo y valida que pase el health check

Si alguno falla, el PR **no se puede mergear**.

---

## 🔧 Mantenimiento

### Actualizar el servidor

```bash
# Jalar la última imagen y recrear el contenedor
docker compose pull
docker compose up -d
```

### Actualizar plugins

Los plugins de Modrinth y los descargados por URL se actualizan automáticamente al reiniciar el contenedor.

Para plugins manuales en `plugins/`, reemplaza el `.jar` y reinicia:

```bash
docker compose restart minecraft
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
> - ❌ No se permite push directo a `main`
> - ✅ Todo merge requiere un PR aprobado por el owner
> - 🔏 Todos los commits deben estar firmados (GPG verified)
> - 🤖 CI debe pasar (el servidor debe levantar correctamente)

---

<div align="center">

**Tilin Server** 🐸 — *¡Bienvenidos a la aventura!*

</div>
