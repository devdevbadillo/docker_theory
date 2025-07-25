# Docker

## Tabla de Contenido

- [Introducción a Docker](#introduccion-docker)
  - [¿Qué es Docker?](#que-es-docker)
  - [Historia y Evolución](#historia-evolucion-docker)
  - [¿Por qué usar Docker?](#por-que-usar-docker)
    - [Beneficios Clave (Consistencia, Aislamiento, Portabilidad, Escalabilidad)](#beneficios-clave-docker)
  - [Docker vs. Máquinas Virtuales](#docker-vs-maquinas-virtuales)
  - [Conceptos Fundamentales](#conceptos-fundamentales-docker)
    
- [Imágenes de Docker](#imagenes-docker)
  - [¿Qué es una Imagen de Docker?](#que-es-imagen-docker)
  - [Capas de Imágenes (Image Layers)](#capas-imagenes)
  - [Registros de Imágenes (Registries)](#registros-imagenes)
    - [Docker Hub](#docker-hub)
    - [Registros Privados](#registros-privados)
  - [Comandos Básicos de Imágenes](#comandos-basicos-imagenes)
    - [`docker pull`: Descargar Imágenes](#docker-pull)
    - [`docker images`: Listar Imágenes](#docker-images)
    - [`docker rmi`: Eliminar Imágenes](#docker-rmi)
    - [`docker history`: Ver Historial de Capas](#docker-history)
    - [`docker tag`: Etiquetar Imágenes](#docker-tag)
    - [`docker push`: Subir Imágenes](#docker-push)

- [Contenedores de Docker](#contenedores-docker)
  - [¿Qué es un Contenedor de Docker?](#que-es-contenedor-docker)
  - [Ciclo de Vida de un Contenedor](#ciclo-vida-contenedor)
  - [Comandos Básicos de Contenedores](#comandos-basicos-contenedores)
    - [`docker run`: Crear y Ejecutar Contenedores](#docker-run)
      - [Modo Detached (`-d`)](#modo-detached)
      - [Asignación de Puertos (`-p`)](#asignacion-puertos)
      - [Variables de Entorno (`-e`)](#variables-entorno-contenedor)
      - [Volúmenes (`-v`)](#volumenes-run)
      - [Nombre del Contenedor (`--name`)](#nombre-contenedor)
      - [Ejecutar Comandos en el Contenedor](#ejecutar-comandos-contenedor)
    - [`docker ps`: Listar Contenedores](#docker-ps)
    - [`docker stop`, `docker start`, `docker restart`: Controlar Contenedores](#docker-stop-start-restart)
    - [`docker rm`: Eliminar Contenedores](#docker-rm)
    - [`docker exec`: Ejecutar Comandos en Contenedores en Ejecución](#docker-exec)
    - [`docker inspect`: Inspeccionar Contenedores](#docker-inspect)
    - [`docker logs`: Ver Logs de Contenedores](#docker-logs)
    - [`docker top`: Ver Procesos en Contenedor](#docker-top)
    - [`docker cp`: Copiar Archivos a/desde Contenedor](#docker-cp)

- [Dockerfile: Construyendo tus Propias Imágenes](#dockerfile)
  - [¿Qué es un Dockerfile?](#que-es-dockerfile)
  - [Instrucciones Clave del Dockerfile](#instrucciones-dockerfile)
    - [`FROM`: Imagen Base](#from-dockerfile)
    - [`RUN`: Ejecutar Comandos](#run-dockerfile)
    - [`CMD`: Comando por Defecto](#cmd-dockerfile)
    - [`ENTRYPOINT`: Punto de Entrada](#entrypoint-dockerfile)
    - [`COPY`: Copiar Archivos/Directorios](#copy-dockerfile)
    - [`ADD`: Añadir Archivos/URLs](#add-dockerfile)
    - [`WORKDIR`: Directorio de Trabajo](#workdir-dockerfile)
    - [`EXPOSE`: Exponer Puertos](#expose-dockerfile)
    - [`ENV`: Variables de Entorno](#env-dockerfile)
    - [`ARG`: Argumentos de Construcción](#arg-dockerfile)
    - [`VOLUME`: Puntos de Montaje de Volumen](#volume-dockerfile)
    - [`USER`: Usuario del Contenedor](#user-dockerfile)
    - [`HEALTHCHECK`: Verificación de Salud](#healthcheck-dockerfile)
  - [Proceso de Construcción (`docker build`)](#proceso-construccion-dockerfile)
  - [Mejores Prácticas para Dockerfiles](#mejores-practicas-dockerfile)
    - [Imágenes Base Ligeras](#imagenes-base-ligeras)
    - [Minimizar Capas](#minimizar-capas)
    - [Multi-Stage Builds](#multi-stage-builds)
    - [Caché de Construcción](#cache-construccion)
    - [Seguridad](#seguridad-dockerfile)

- [Persistencia de Datos (Volúmenes)](#persistencia-datos-docker)
  - [¿Por qué la Persistencia?](#por-que-persistencia)
  - [Tipos de Volúmenes](#tipos-volumenes-docker)
    - [Volúmenes Nombrados (Named Volumes)](#volumenes-nombrados-docker)
      - [Creación y Gestión (`docker volume`)](#creacion-gestion-volumenes)
    - [Bind Mounts](#bind-mounts-docker)
    - [tmpfs Mounts](#tmpfs-mounts)
  - [Uso de Volúmenes en Contenedores](#uso-volumenes-contenedores)
  - [Inspeccionar Volúmenes](#inspeccionar-volumenes)
  - [Casos de Uso Comunes](#casos-uso-volumenes)

- [Redes en Docker](#redes-docker)
  - [Conceptos de Redes de Contenedores](#conceptos-redes-contenedores-docker)
  - [Tipos de Redes](#tipos-redes-docker)
    - [`bridge` (Por Defecto)](#bridge-network)
    - [`host` Network](#host-network)
    - [`none` Network](#none-network)
    - [`overlay` Network (para Swarm)](#overlay-network-docker)
  - [Crear Redes Personalizadas (`docker network create`)](#crear-redes-personalizadas)
  - [Conectar Contenedores a Redes](#conectar-contenedores-redes)
  - [DNS en Docker](#dns-docker)
  - [Publicación de Puertos (`-p`, `--publish`)](#publicacion-puertos-docker)
  - [Vínculo entre Contenedores (`--link` - Legado)](#vinculo-contenedores-legado)

- [Docker Compose: Orquestación Local](#docker-compose)
  - [¿Qué es Docker Compose?](#que-es-docker-compose)
  - [Archivo `docker-compose.yml`](#docker-compose-yml)
    - [Versiones del Formato](#versiones-formato-compose)
    - [Servicios (`services`)](#servicios-compose)
    - [Redes (`networks`)](#redes-compose)
    - [Volúmenes (`volumes`)](#volumenes-compose)
  - [Comandos Básicos de Compose](#comandos-basicos-compose)
    - [`docker compose up`: Levantar Aplicaciones](#docker-compose-up)
    - [`docker compose down`: Bajar Aplicaciones](#docker-compose-down)
    - [`docker compose ps`: Listar Servicios](#docker-compose-ps)
    - [`docker compose logs`: Ver Logs](#docker-compose-logs)
    - [`docker compose exec`: Ejecutar Comandos](#docker-compose-exec)
    - [`docker compose build`: Reconstruir Imágenes](#docker-compose-build)
  - [Casos de Uso de Docker Compose](#casos-uso-compose)

- [Docker Hub y Registros de Contenedores](#docker-hub-registros)
  - [Docker Hub: El Registro por Defecto](#docker-hub-default)
  - [Cuentas y Repositorios](#cuentas-repositorios-docker-hub)
  - [Automatización de Builds (Automated Builds)](#automatizacion-builds)
  - [Registros Privados (Private Registries)](#registros-privados-docker)
  - [Autenticación (`docker login`)](#docker-login)

- [Seguridad en Docker](#seguridad-docker)
  - [Principios de Seguridad de Contenedores](#principios-seguridad-contenedores)
  - [Imágenes Seguras](#imagenes-seguras-docker)
    - [Minimizar la Superficie de Ataque](#minimizar-superficie-ataque)
    - [Escaneo de Vulnerabilidades](#escaneo-vulnerabilidades)
  - [Contenedores Seguros](#contenedores-seguros-docker)
    - [Principio de Menor Privilegio (Non-root user)](#menor-privilegio-docker)
    - [Restricción de Recursos (CPU, Memoria)](#restriccion-recursos-docker)
    - [Capacidades de Linux (Capabilities)](#capacidades-linux-docker)
    - [Seccomp y AppArmor](#seccomp-apparmor)
  - [Gestión de Secretos](#gestion-secretos-docker)
  - [Redes Seguras](#redes-seguras-docker)

- [Optimización de Rendimiento y Troubleshooting](#optimizacion-rendimiento-troubleshooting)
  - [Optimización de Rendimiento](#optimizacion-rendimiento-docker)
    - [Uso Eficiente de Recursos](#uso-eficiente-recursos-docker)
    - [Optimización de I/O](#optimizacion-io-docker)
    - [Caché de Capas de Dockerfile](#cache-capas-dockerfile-opt)
  - [Troubleshooting Común](#troubleshooting-comun-docker)
    - [Contenedores que no Inician](#contenedores-no-inician)
    - [Problemas de Red](#problemas-red-docker-troubleshoot)
    - [Problemas de Volumen](#problemas-volumen-docker)
    - [Errores de Construcción de Imágenes](#errores-construccion-imagenes)
    - [Uso de `docker events`, `docker system df`, `docker prune`](#docker-events-system-df-prune)


<a id="introduccion-docker"></a>
## Introducción a Docker

<a id="que-es-docker"></a>
### ¿Qué es Docker?
<a id="historia-evolucion-docker"></a>
### Historia y Evolución
<a id="por-que-usar-docker"></a>
### ¿Por qué usar Docker?
<a id="beneficios-clave-docker"></a>
#### Beneficios Clave (Consistencia, Aislamiento, Portabilidad, Escalabilidad)
<a id="docker-vs-maquinas-virtuales"></a>
### Docker vs. Máquinas Virtuales
<a id="conceptos-fundamentales-docker"></a>
### Conceptos Fundamentales

<a id="instalacion-configuracion-docker"></a>
## Instalación y Configuración de Docker
<a id="requisitos-sistema-docker"></a>
### Requisitos del Sistema
<a id="instalacion-plataformas-docker"></a>
### Instalación en Diferentes Plataformas (Linux, Windows, macOS)
<a id="verificacion-instalacion-docker"></a>
### Verificación de la Instalación
<a id="configuracion-post-instalacion-docker"></a>
### Configuración Post-Instalación (Usuario sin `sudo`, Daemon)

<a id="imagenes-docker"></a>
## Imágenes de Docker

<a id="que-es-imagen-docker"></a>
### ¿Qué es una Imagen de Docker?
<a id="capas-imagenes"></a>
### Capas de Imágenes (Image Layers)
<a id="registros-imagenes"></a>
### Registros de Imágenes (Registries)
<a id="docker-hub"></a>
#### Docker Hub
<a id="registros-privados"></a>
#### Registros Privados
<a id="comandos-basicos-imagenes"></a>
### Comandos Básicos de Imágenes
<a id="docker-pull"></a>
#### `docker pull`: Descargar Imágenes
<a id="docker-images"></a>
#### `docker images`: Listar Imágenes
<a id="docker-rmi"></a>
#### `docker rmi`: Eliminar Imágenes
<a id="docker-history"></a>
#### `docker history`: Ver Historial de Capas
<a id="docker-tag"></a>
#### `docker tag`: Etiquetar Imágenes
<a id="docker-push"></a>
#### `docker push`: Subir Imágenes

<a id="contenedores-docker"></a>
## Contenedores de Docker

<a id="que-es-contenedor-docker"></a>
### ¿Qué es un Contenedor de Docker?
<a id="ciclo-vida-contenedor"></a>
### Ciclo de Vida de un Contenedor
<a id="comandos-basicos-contenedores"></a>
### Comandos Básicos de Contenedores
<a id="docker-run"></a>
#### `docker run`: Crear y Ejecutar Contenedores
<a id="modo-detached"></a>
##### Modo Detached (`-d`)
<a id="asignacion-puertos"></a>
##### Asignación de Puertos (`-p`)
<a id="variables-entorno-contenedor"></a>
##### Variables de Entorno (`-e`)
<a id="volumenes-run"></a>
##### Volúmenes (`-v`)
<a id="nombre-contenedor"></a>
##### Nombre del Contenedor (`--name`)
<a id="ejecutar-comandos-contenedor"></a>
##### Ejecutar Comandos en el Contenedor
<a id="docker-ps"></a>
#### `docker ps`: Listar Contenedores
<a id="docker-stop-start-restart"></a>
#### `docker stop`, `docker start`, `docker restart`: Controlar Contenedores
<a id="docker-rm"></a>
#### `docker rm`: Eliminar Contenedores
<a id="docker-exec"></a>
#### `docker exec`: Ejecutar Comandos en Contenedores en Ejecución
<a id="docker-inspect"></a>
#### `docker inspect`: Inspeccionar Contenedores
<a id="docker-logs"></a>
#### `docker logs`: Ver Logs de Contenedores
<a id="docker-top"></a>
#### `docker top`: Ver Procesos en Contenedor
<a id="docker-cp"></a>
#### `docker cp`: Copiar Archivos a/desde Contenedor

<a id="dockerfile"></a>
## Dockerfile: Construyendo tus Propias Imágenes

<a id="que-es-dockerfile"></a>
### ¿Qué es un Dockerfile?
<a id="instrucciones-dockerfile"></a>
### Instrucciones Clave del Dockerfile
<a id="from-dockerfile"></a>
#### `FROM`: Imagen Base
<a id="run-dockerfile"></a>
#### `RUN`: Ejecutar Comandos
<a id="cmd-dockerfile"></a>
#### `CMD`: Comando por Defecto
<a id="entrypoint-dockerfile"></a>
#### `ENTRYPOINT`: Punto de Entrada
<a id="copy-dockerfile"></a>
#### `COPY`: Copiar Archivos/Directorios
<a id="add-dockerfile"></a>
#### `ADD`: Añadir Archivos/URLs
<a id="workdir-dockerfile"></a>
#### `WORKDIR`: Directorio de Trabajo
<a id="expose-dockerfile"></a>
#### `EXPOSE`: Exponer Puertos
<a id="env-dockerfile"></a>
#### `ENV`: Variables de Entorno
<a id="arg-dockerfile"></a>
#### `ARG`: Argumentos de Construcción
<a id="volume-dockerfile"></a>
#### `VOLUME`: Puntos de Montaje de Volumen
<a id="user-dockerfile"></a>
#### `USER`: Usuario del Contenedor
<a id="healthcheck-dockerfile"></a>
#### `HEALTHCHECK`: Verificación de Salud
<a id="proceso-construccion-dockerfile"></a>
### Proceso de Construcción (`docker build`)
<a id="mejores-practicas-dockerfile"></a>
### Mejores Prácticas para Dockerfiles
<a id="imagenes-base-ligeras"></a>
#### Imágenes Base Ligeras
<a id="minimizar-capas"></a>
#### Minimizar Capas
<a id="multi-stage-builds"></a>
#### Multi-Stage Builds
<a id="cache-construccion"></a>
#### Caché de Construcción
<a id="seguridad-dockerfile"></a>
#### Seguridad

<a id="persistencia-datos-docker"></a>
## Persistencia de Datos (Volúmenes)

<a id="por-que-persistencia"></a>
### ¿Por qué la Persistencia?
<a id="tipos-volumenes-docker"></a>
### Tipos de Volúmenes
<a id="volumenes-nombrados-docker"></a>
#### Volúmenes Nombrados (Named Volumes)
<a id="creacion-gestion-volumenes"></a>
##### Creación y Gestión (`docker volume`)
<a id="bind-mounts-docker"></a>
#### Bind Mounts
<a id="tmpfs-mounts"></a>
#### tmpfs Mounts
<a id="uso-volumenes-contenedores"></a>
### Uso de Volúmenes en Contenedores
<a id="inspeccionar-volumenes"></a>
### Inspeccionar Volúmenes
<a id="casos-uso-volumenes"></a>
### Casos de Uso Comunes

<a id="redes-docker"></a>
## Redes en Docker

<a id="conceptos-redes-contenedores-docker"></a>
### Conceptos de Redes de Contenedores
<a id="tipos-redes-docker"></a>
### Tipos de Redes
<a id="bridge-network"></a>
#### `bridge` (Por Defecto)
<a id="host-network"></a>
#### `host` Network
<a id="none-network"></a>
#### `none` Network
<a id="overlay-network-docker"></a>
#### `overlay` Network (para Swarm)
<a id="crear-redes-personalizadas"></a>
### Crear Redes Personalizadas (`docker network create`)
<a id="conectar-contenedores-redes"></a>
### Conectar Contenedores a Redes
<a id="dns-docker"></a>
### DNS en Docker
<a id="publicacion-puertos-docker"></a>
### Publicación de Puertos (`-p`, `--publish`)
<a id="vinculo-contenedores-legado"></a>
### Vínculo entre Contenedores (`--link` - Legado)

<a id="docker-compose"></a>
## Docker Compose: Orquestación Local

<a id="que-es-docker-compose"></a>
### ¿Qué es Docker Compose?
<a id="docker-compose-yml"></a>
### Archivo `docker-compose.yml`
<a id="versiones-formato-compose"></a>
#### Versiones del Formato
<a id="servicios-compose"></a>
#### Servicios (`services`)
<a id="redes-compose"></a>
#### Redes (`networks`)
<a id="volumenes-compose"></a>
#### Volúmenes (`volumes`)
<a id="comandos-basicos-compose"></a>
### Comandos Básicos de Compose
<a id="docker-compose-up"></a>
#### `docker compose up`: Levantar Aplicaciones
<a id="docker-compose-down"></a>
#### `docker compose down`: Bajar Aplicaciones
<a id="docker-compose-ps"></a>
#### `docker compose ps`: Listar Servicios
<a id="docker-compose-logs"></a>
#### `docker compose logs`: Ver Logs
<a id="docker-compose-exec"></a>
#### `docker compose exec`: Ejecutar Comandos
<a id="docker-compose-build"></a>
#### `docker compose build`: Reconstruir Imágenes
<a id="casos-uso-compose"></a>
### Casos de Uso de Docker Compose

<a id="docker-hub-registros"></a>
## Docker Hub y Registros de Contenedores

<a id="docker-hub-default"></a>
### Docker Hub: El Registro por Defecto
<a id="cuentas-repositorios-docker-hub"></a>
### Cuentas y Repositorios
<a id="automatizacion-builds"></a>
### Automatización de Builds (Automated Builds)
<a id="registros-privados-docker"></a>
### Registros Privados (Private Registries)
<a id="docker-login"></a>
### Autenticación (`docker login`)

<a id="seguridad-docker"></a>
## Seguridad en Docker

<a id="principios-seguridad-contenedores"></a>
### Principios de Seguridad de Contenedores
<a id="imagenes-seguras-docker"></a>
### Imágenes Seguras
<a id="minimizar-superficie-ataque"></a>
#### Minimizar la Superficie de Ataque
<a id="escaneo-vulnerabilidades"></a>
#### Escaneo de Vulnerabilidades
<a id="contenedores-seguros-docker"></a>
### Contenedores Seguros
<a id="menor-privilegio-docker"></a>
#### Principio de Menor Privilegio (Non-root user)
<a id="restriccion-recursos-docker"></a>
#### Restricción de Recursos (CPU, Memoria)
<a id="capacidades-linux-docker"></a>
#### Capacidades de Linux (Capabilities)
<a id="seccomp-apparmor"></a>
#### Seccomp y AppArmor
<a id="gestion-secretos-docker"></a>
### Gestión de Secretos
<a id="redes-seguras-docker"></a>
### Redes Seguras

<a id="optimizacion-rendimiento-troubleshooting"></a>
## Optimización de Rendimiento y Troubleshooting

<a id="optimizacion-rendimiento-docker"></a>
### Optimización de Rendimiento
<a id="uso-eficiente-recursos-docker"></a>
#### Uso Eficiente de Recursos
<a id="optimizacion-io-docker"></a>
#### Optimización de I/O
<a id="cache-capas-dockerfile-opt"></a>
#### Caché de Capas de Dockerfile
<a id="troubleshooting-comun-docker"></a>
### Troubleshooting Común
<a id="contenedores-no-inician"></a>
#### Contenedores que no Inician
<a id="problemas-red-docker-troubleshoot"></a>
#### Problemas de Red
<a id="problemas-volumen-docker"></a>
#### Problemas de Volumen
<a id="errores-construccion-imagenes"></a>
#### Errores de Construcción de Imágenes
<a id="docker-events-system-df-prune"></a>
#### Uso de `docker events`, `docker system df`, `docker prune`
