# metabase

Entorno de build en contenedor para compilar Metabase desde su código fuente (v1.37.0.2) con Clojure y Yarn.

## Visibilidad y rama

- Público.
- Rama por defecto: `main` (según git local).

## Stack

- Docker/Podman (Dockerfiles sin extensión).
- Java/Clojure (Leiningen, Clojure CLI), Node.js 20 y Yarn.
- No hay código propio del proyecto: el código de Metabase se clona en tiempo de build.

## Estructura

- `Base`: Dockerfile de imagen base de Ubuntu con `openjdk-11-jdk`, git, curl y vim. Ejecuta `sleep infinity`.
- `Source`: Dockerfile que parte de `dev-jcs`, instala Node 20, Leiningen, Yarn y Clojure CLI, clona Metabase `v1.37.0.2` en `/build` y ejecuta `yarn && yarn build`. Expone el puerto 3000.

## Comandos

Del contenido de los Dockerfiles (no ejecutados):

- Construir la base: `podman build -f Base -t dev-jcs .` (nombre de imagen asumido, no verificado).
- Construir la imagen de Metabase: `podman build -f Source -t metabase-build .` (nombre asumido).
- Los repos no tienen Makefile ni scripts.
- Tests: no hay.

## Configuración

- `ENV DEBIAN_FRONTEND=noninteractive`, `NODE_OPTIONS=--openssl-legacy-provider`, `NODE_ENV=oss`.
- Versión de Metabase fijada en la línea `git clone --branch v1.37.0.2`.
- Nodo: `setup_20.x` (hay una línea comentada con `setup_18.x`).

## Convenciones para agentes

- Los Dockerfiles se llaman `Base` y `Source`, no `Dockerfile`: pasar `-f` explícitamente.
- Mantener la versión de Metabase fijada; cambiarla implica revisar dependencias de Node/Clojure.
- Los servicios se levantan con `podman`, no instalando nada en el host.

## Estado

- Sin tests ni CI.
- Atención: `Source` depende de la imagen `dev-jcs`, que no está definida en el repo; hay que construirla antes.
- Atención: `Base` y `Source` ejecutan `apt upgrade` y curl a `setup_20.x` y a un instalador remoto de Clojure sin verificación de checksum.
- Hay código comentado (`setup_18.x`, `git clone` de `v0.48.8`).
- `NODE_OPTIONS=--openssl-legacy-provider` indica dependencias antiguas de webpack; no verificado.
