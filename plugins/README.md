# 📦 Plugins personalizados

Este directorio es para colocar archivos `.jar` de plugins adicionales que no estén disponibles en [Modrinth](https://modrinth.com/) ni como descarga directa por URL.

## Plugins principales (descarga automática)

Los siguientes plugins se descargan automáticamente al iniciar el contenedor, configurados en `docker-compose.yml`:

| Plugin | Fuente | Configuración |
|---|---|---|
| **EssentialsX** | Modrinth (slug: `essentialsx`) | `MODRINTH_PROJECTS` |
| **Vault** | GitHub Releases (URL directa) | `PLUGINS` |
| **LuckPerms** | Modrinth (slug: `luckperms`) | `MODRINTH_PROJECTS` |

> **No es necesario** descargar manualmente estos plugins. El servidor los obtiene de forma automática cada vez que se inicia.

## ¿Cuándo usar este directorio?

Coloca aquí únicamente archivos `.jar` de plugins que **no estén disponibles** en Modrinth ni como descarga directa por URL. Por ejemplo:

- Plugins privados o de desarrollo propio.
- Plugins descargados desde fuentes externas (Jenkins, GitHub Releases, etc.).
- Versiones específicas que no se encuentren en los repositorios soportados.

## ¿Cómo funciona?

1. Este directorio se monta como **solo lectura** en el contenedor (`./plugins:/plugins:ro`).
2. La variable de entorno `COPY_PLUGINS_DEST` (definida en `docker-compose.yml`) indica al servidor que copie el contenido de `/plugins` hacia `/data/plugins` dentro del contenedor.
3. Los plugins copiados se cargan junto con los descargados automáticamente al iniciar el servidor.

## Control de versiones

Los archivos `.jar` están **excluidos de git** mediante `.gitignore` para evitar versionar binarios pesados. Solo se versiona este `README.md` y el archivo `.gitkeep` que mantiene el directorio en el repositorio.

## Compatibilidad

> ⚠️ **Importante:** Todos los plugins que coloques aquí deben ser compatibles con **Paper 1.21.4**. Verifica la compatibilidad antes de añadir cualquier `.jar` para evitar errores al iniciar el servidor.