# Docker

## Tabla de Contenido

- [Fundamentos de Docker](#fundamentos-de-contenedores)
  - [¿Qué es Docker?](#que-es-docker)
    - [Problemas que resuelve](#problemas-que-resuelve-docker)
  - [Arquitectura de Docker](#arquitectura-de-docker)
    - [Docker Demon](#docker-demon)
    - [Docker CLI](#docker-cli)
    - [Docker Host](#docker-host)
    - [Docker Registry](#docker-registry)
  - [¿Qué es un contenedor?](#que-es-un-contenedor)
    - [Diferencia entre Contenedores y Máquinas Virtuales (VMs)](#contenedores-vs-vms)
  - [Espacios de Nombres (Namespaces)](#namespace)
    - [Tipos de Namespace](#tipos-de-namespace)
  - [Grupos de control (cgroups)](#cgroups)
  - [Union File Systems (UnionFS)](#unionfs)
- [Imágenes de Docker](#imagenes-de-docker)
  - [¿Qué es una Imagen de Docker?](#que-es-una-imagen-de-docker)
  - [Dockerfiles](#dockerfiles)
    - [Imágen base vs Imágen derivada](#imagen-base-vs-derivada)
    - [Multistage builds](#multistage-builds)
  - [Capas de imágenes](#capas-de-imagenes)
  - [Registro de contenedores](#registro-de-contenedores)
  - [Etiquetado de imágenes](#etiquetado-de-imagenes)
  - [Ciclo de vida de un contenedor](#ciclo-de-vida-de-un-contenedor)
- [Redes en Docker](#redes-en-docker)
  - [Tipos de redes en Docker](#tipos-de-redes)
  - [Publicación de puertos](#publicacion-de-puertos)
  - [DNS y descubrimiento de servicios](#dns-y-descubrimiento-de-servicios)
- [Almacenamiento en Docker](#almacenamiento-en-docker)
  - [Volúmenes (volumes)](#volumes)
  - [Backups de datos de contenedores](#backups-en-contenedores)
  - [Mount Bindings](#mount-bindings)
  - [tmpfs Mounts](#tmpfs-mounts)
  - [Drivers de almacenamiento](#drivers-de-almacenamiento)


<a id="fundamentos-de-contenedores"></a>
## Fundamentos de Docker


<a id="que-es-docker"></a>
### ¿Qué es docker?

Docker es una plataforma de código abierto que **facilita la creación, el despliegue y la ejecución de aplicaciones** utilizando contenedores. En esencia, **Docker permite "empaquetar" una aplicación y todas sus dependencias** (bibliotecas, configuraciones, etc.) en un paquete estandarizado **llamado contenedor**.

> [!NOTE]
> Un contenedor puede ejecutarse en **cualquier entorno que tenga instalado Docker**, garantizando que la aplicación se comporte de manera consistente, independientemente de dónde se ejecute.

Docker estandariza el empaquetado y transporte de software, permitiendo que cualquier aplicación se ejecute en cualquier entorno sin importar sus dependencias.

<a id="problemas-que-resuelve-docker"></a>
#### Problemas que resuelve

Antes de Docker, los desarrolladores a menudo se enfrentaban a un problema recurrente conocido como "funciona en mi máquina". Esto ocurría porque las aplicaciones dependían de un entorno específico (versiones de bibliotecas, configuraciones del sistema operativo, etc.) que podía variar entre el entorno de desarrollo, el de pruebas y el de producción. Docker resuelve esto de varias maneras: 

1. `Gestión de Dependencias`: Cada aplicación puede tener sus propias versiones de bibliotecas y tiempos de ejecución sin interferir con otras aplicaciones en el mismo servidor. **Docker aísla** estas dependencias **dentro del contenedor**. Por ejemplo, puedes ejecutar una aplicación que requiere Python 2.7 y otra que requiere Python 3.9 en el mismo servidor sin conflictos.

2. `Despliegue Rápido y Fiable`: Los contenedores son ligeros y se inician en segundos, lo que permite un despliegue y escalado rápidos. Además, al ser paquetes inmutables, la probabilidad de errores de despliegue se reduce significativamente.

3. `Aislamiento de Aplicaciones`: **Cada contenedor funciona como un proceso aislado**. Si una aplicación en un contenedor falla, no afecta a otras aplicaciones que se ejecutan en otros contenedores en el mismo host.

4. `Portabilidad`: Los contenedores son altamente portables. Una imagen de Docker puede ser compartida y ejecutada en cualquier máquina con Docker, ya sea en un servidor local, una máquina virtual o en la nube


<a id="arquitectura-de-docker"></a>
### Arquitectura de Docker

La arquitectura de Docker **se basa en un modelo cliente-servidor**. El cliente Docker (Docker CLI) se comunica con el demonio Docker **(Docker Daemon), que es el encargado de construir, ejecutar y gestionar los contenedores**. 

<a id="docker-demon"></a>
#### Docker demon

El Docker Daemon, también conocido como **dockerd**, es el "cerebro" de Docker. Es un proceso persistente que se ejecuta en el sistema operativo host. Sus responsabilidades principales incluyen:

 * `Construir Imágenes`: Utiliza los Dockerfiles para crear imágenes de Docker.
 * `Ejecutar Contenedores`: Inicia, detiene y gestiona el ciclo de vida de los contenedores.
 * `Gestionar Redes`: Configura y administra las redes virtuales para los contenedores.
 * `Gestionar Volúmenes de Datos`: Administra el almacenamiento persistente para los contenedores.
 * `Autenticación y Autorización`: Maneja la comunicación segura con Docker Registries.
 * `API REST`: Expone una API RESTful para que el cliente Docker (y otras herramientas) puedan interactuar con él.


<a id="docker-cli"></a>
#### Docker CLI (Command Line Interface)

El Docker CLI es la interfaz de línea de comandos que los usuarios **utilizan para interactuar con el Docker Daemon**. Cuando se escriben comandos como docker run, docker build, docker ps, etc., el CLI **convierte estos comandos en solicitudes a la API REST del Docker Daemon**.

> [!IMPORTANT]
> El CLI puede comunicarse con un Docker Daemon que se ejecuta en la misma máquina o en una máquina remota, lo que facilita la gestión de entornos distribuidos.


<a id="docker-host"></a>
#### Docker Host

El Docker Host es la **máquina física o virtual donde se ejecuta el Docker Daemon**. Es el entorno subyacente que **proporciona los recursos (CPU, memoria, almacenamiento, red)** para que los contenedores se ejecuten.

> [!NOTE]
> El Docker Host puede ser un servidor Linux, Windows o incluso macOS (a través de una máquina virtual ligera).
> Los contenedores que se ejecutan en un Docker Host comparten el kernel del sistema operativo del host.

<a id="docker-registry"></a>
#### Docker Registry

Un Docker Registry **es un servicio de almacenamiento y distribución para imágenes de Docker**. Es como un repositorio central donde se pueden subir imágenes propias o descargar imágenes preexistentes de otros.

* `Docker Hub`: Es el registro público más grande y conocido, operado por Docker. Contiene una vasta colección de imágenes oficiales (como Ubuntu, Nginx, Node.js) y miles de imágenes creadas por la comunidad.
* `Registros Privados`: Las organizaciones pueden configurar sus propios registros privados para almacenar imágenes internas, garantizando mayor seguridad y control.


> Flujo general

1. Un desarrollador escribe un Dockerfile para definir cómo construir su aplicación.
2. El Docker CLI envía el comando docker build al Docker Daemon.
3. El Docker Daemon construye una imagen a partir del Dockerfile.
4. El desarrollador puede opcionalmente push (subir) esa imagen a un Docker Registry.
5. Otro usuario (o el mismo desarrollador en otro entorno) puede pull (descargar) la imagen del Docker Registry.
6. El Docker CLI envía el comando docker run al Docker Daemon.
7. El Docker Daemon utiliza la imagen para crear y ejecutar un contenedor en el Docker Host.

![image](https://github.com/user-attachments/assets/76555787-47aa-4026-bc77-22004fba93b9)


<a id="que-es-un-contenedor"></a>
### ¿Qué es un Contenedor?
Un contenedor **es una unidad de software** estandarizada y autónoma que **empaqueta todo lo necesario para ejecutar una aplicación**: el código, el entorno de ejecución, las bibliotecas del sistema, las herramientas del sistema y la configuración. 

> [!IMPORTANT]
> Es una instancia ejecutable de una imagen de Docker.

Una vez que un contenedor se inicia a partir de una imagen, **los cambios dentro de él son temporales** a menos que se persistan explícitamente en volúmenes. Esto promueve la idea de infraestructura inmutable, donde **los contenedores se reemplazan en lugar de modificarse**.

<a id="contenedores-vs-vms"></a>
#### Diferencia entre Contenedores y Máquinas Virtuales (VMs)
Aunque ambos proporcionan ambientes aislados para ejecutar aplicaciones, su arquitectura y eficiencia son fundamentalmente diferentes:

| **Característica**        | **Máquinas Virtuales (VMs)**                                                              | **Contenedores**                                                                 |
|--------------------------|--------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
| **Arquitectura**         | Se ejecutan sobre un Hypervisor (VMware, VirtualBox, Hyper-V)                              | Se ejecutan sobre un Motor de Contenedores (Docker Engine)                      |
| **Sistema Operativo**    | Cada VM tiene su propio Sistema Operativo Invitado (Guest OS) completo.                    | Comparten el kernel del Sistema Operativo Host.                                 |
| **Kernel**               | Propio kernel para cada VM.                                                                | Comparten el kernel del Host.                                                   |
| **Tamaño**               | Gigabytes (GBs), ya que incluyen un SO completo.                                           | Megabytes (MBs), mucho más ligeros.                                             |
| **Tiempo de Arranque**   | Minutos, debido a la carga del SO invitado.                                                | Segundos o milisegundos.                                                        |
| **Aislamiento**          | Alto nivel de aislamiento a nivel de hardware virtualizado.                                | Aislamiento a nivel de proceso y sistema de archivos (namespaces y cgroups).    |
| **Uso de Recursos**      | Más intensivo en recursos (CPU, RAM, Disco) debido al SO completo por VM.                  | Muy eficiente en recursos, ya que el SO se comparte.                            |
| **Portabilidad**         | Moderada, pueden necesitar adaptación al hypervisor.                                       | Alta, se ejecutan de manera consistente en cualquier host Docker.               |
| **Casos de Uso Típicos** | Ejecución de diferentes sistemas operativos, entornos muy aislados, consolidación de servidores. | Despliegue de microservicios, DevOps, ambientes de desarrollo/pruebas consistentes. |

![image](https://github.com/user-attachments/assets/ad350598-6e40-4295-83cf-17feef3b4838)

En resumen, las VMs virtualizan el hardware completo y necesitan un sistema operativo para cada una, mientras que los contenedores virtualizan a nivel del sistema operativo, compartiendo el kernel del host. Esto hace que los contenedores sean mucho más eficientes y ligeros para empaquetar y ejecutar aplicaciones.

<a id="namespace"></a>
### Espacios de Nombres (Namespaces)

Los Namespaces (Espacios de Nombres) son una característica del kernel de Linux que **permite particionar los recursos del sistema** de modo que un grupo de procesos tenga su propia vista aislada de un recurso global.

Docker utiliza varios tipos de namespaces para proporcionar el aislamiento que experimentan los contenedores. Podríamos hacer la analogía que cada namespace es una cabina de control separada en un avión, donde cada piloto (proceso) ve y controla sus propios instrumentos, aunque todos comparten el mismo avión (kernel).

<a id="tipos-de-namespace"></a>
#### Tipos de Namespace

> MNT Namespace (Mount Namespace)

* `Funcionamiento`: Permite que cada contenedor tenga su propia jerarquía de puntos de montaje, lo que significa que un proceso en un contenedor solo ve los archivos y directorios que se han montado en su namespace, sin acceder al sistema de archivos del host o de otros contenedores a menos que se le dé acceso explícito.
* `Impacto en Docker`: Cuando un contenedor arranca, se le asigna un sistema de archivos raíz (basado en la imagen). Este es un sistema de archivos completamente independiente del host, lo que contribuye en gran medida al aislamiento
  
> IPC Namespace (Interprocess Communication Namespace)

* `Funcionamiento`: Incluye mecanismos como colas de mensajes, semáforos y memoria compartida. Al aislar el IPC, los procesos dentro de un contenedor no pueden interferir con los mecanismos IPC de otros contenedores o del host.
* `Impacto en Docker`: Mejora la seguridad y la estabilidad, evitando que un contenedor malicioso o erróneo manipule la comunicación de otros procesos.


> [!NOTE]
> Los namespaces son el mecanismo principal que Docker utiliza para lograr el aislamiento a nivel de proceso y sistema de archivos, haciendo que **cada contenedor parezca un mini-sistema operativo independiente**.



<a id="cgroups"></a>
### Grupos de Control (cgroups)
Mientras que los Namespaces proporcionan aislamiento, los cgroups (Control Groups) son una característica del kernel de Linux que **se encarga de la limitación y contabilidad de recursos**. Su propósito es permitir que se asignen, prioricen, limiten y midan el uso de recursos de un grupo de procesos.


Podríamos imaginar a los cgroups como la gerencia de recursos de un edificio: asignan la cantidad de electricidad, agua o espacio de oficina que cada departamento (grupo de procesos) puede utilizar, asegurando que ninguno monopolice los recursos.


Los recursos principales que los cgroups pueden controlar incluyen:

1. **CPU**

 * `Funcionamiento`: Se puede limitar la cantidad de ciclos de CPU que un contenedor puede usar (ej. 50% de un núcleo, 2 núcleos dedicados) o establecer prioridades (ej. un contenedor tiene el doble de prioridad de CPU que otro si hay contención).
 * `Impacto en Docker`: Evita que un contenedor "codicioso" consuma todos los recursos de la CPU del host, impactando negativamente a otros contenedores o al propio sistema operativo del host.

2. **Memoria (Memory)**:

* `Funcionamiento`: Puedes establecer límites máximos de memoria (RAM) que un contenedor puede consumir. Si un contenedor intenta usar más memoria de la asignada, el kernel puede terminar el proceso (OOM Killer) o, en algunos casos, el contenedor podría congelarse.
* `Impacto en Docker`: Previene que un contenedor con fugas de memoria o una alta demanda inesperada de memoria agote la RAM del host, lo que podría llevar a la inestabilidad del sistema.

Mientras los namespaces aíslan el "qué ve" un contenedor, los cgroups controlan el "cuánto puede usar" un contenedor de los recursos del host. Juntos, proporcionan el aislamiento y la gestión de recursos que definen un contenedor.


<a id="unionfs"></a>
### Union File Systems (UnionFS)
Los Union File Systems (UnionFS) son sistemas de archivos que permiten **superponer directorios de diferentes ubicaciones**, haciendo que parezcan un único directorio unificado. **En el contexto de Docker**, UnionFS (o sus implementaciones como OverlayFS, AUFS, Btrfs, Device Mapper) **es fundamental para cómo se construyen y ejecutan las imágenes y contenedores**.

> Funcionamiento de UnionFS en Docker

1. Imágenes en Capas
    
- Una imagen de Docker no es un archivo monolítico, sino que se compone de una serie de capas de solo lectura. Cada instrucción en un Dockerfile (como FROM, RUN, COPY, ADD) crea una nueva capa en la imagen. **Estas capas se apilan unas sobre otras. Cuando se accede a un archivo, Docker busca en la capa superior; si no lo encuentra, baja a la siguiente capa y así sucesivamente**.

> [!NOTE]
>  Si dos imágenes comparten la misma capa base (por ejemplo, ubuntu:latest), solo se descarga y almacena una vez en el disco del host. Esto ahorra espacio en disco y ancho de banda.
>  Las capas base de la imagen son inmutables. Una vez creadas, no cambian.


2. Capa de Escritura de Contenedores (Container Layer):

- Cuando se inicia un contenedor a partir de una imagen, Docker añade una capa de escritura (writable layer) en la parte superior de la pila de capas de la imagen. Todos los cambios realizados por el contenedor (nuevos archivos, modificaciones de archivos existentes, eliminaciones) se escriben en esta capa superior de escritura.
  
- Copy-on-Write (CoW): Este es un principio clave. Cuando un contenedor necesita modificar un archivo que existe en una de las capas de solo lectura inferiores, Docker no modifica directamente esa capa. En su lugar, copia el archivo de la capa inferior a la capa de escritura del contenedor, y luego la modificación se realiza en la copia. La versión original en la capa inferior permanece sin cambios.

- Aislamiento y Limpieza: Si eliminas el contenedor, esta capa de escritura se elimina, y todos los cambios no persistentes se pierden. Las capas de la imagen subyacente permanecen intactas para futuros contenedores.

> Ventajas de UnionFS en Docker:
>
> 1. `Optimización del Almacenamiento`: Múltiples contenedores que usan la misma imagen base solo almacenan esa imagen base una vez en el disco, ahorrando mucho espacio.
> 2. `Distribución Eficiente`: Al descargar una imagen, solo se descargan las capas que no se tienen localmente, lo que acelera las descargas.
> 3. `Construcción de Imágenes Rápida`: Al construir nuevas imágenes con Dockerfiles, Docker solo necesita construir y añadir las capas que son nuevas, reutilizando las capas existentes si las instrucciones no han cambiado.

En resumen, UnionFS es la tecnología que permite a Docker construir imágenes en capas, compartirlas eficientemente, y proporcionar una capa de escritura efímera para cada contenedor, lo que contribuye a la ligereza, velocidad y portabilidad de los contenedores.

<a id="imagenes-de-docker"></a>
## Imágenes de Docker
El propósito principal de una imagen de Docker es proporcionar un entorno ejecutable consistente y portátil para una aplicación, garantizando que se ejecutará de la misma manera en cualquier lugar donde se despliegue.

<a id="que-es-una-imagen-de-docker"></a>
### ¿Qué es una Imagen de Docker?

Una Imagen de Docker es **una plantilla de solo lectura** que contiene un conjunto de **instrucciones para crear un contenedor**. Pensemos en ella como una "clase" en programación orientada a objetos, mientras que un contenedor sería una "instancia" de esa clase.

Más específicamente, una imagen incluye:

- `Un sistema de archivos raíz`: Una instantánea del sistema de archivos de un sistema operativo (por ejemplo, una versión mínima de Ubuntu o Alpine Linux).
- `El código de tu aplicación`: Tus archivos ejecutables, scripts, etc.
- `Bibliotecas, dependencias y herramientas`: Todas las librerías de terceros y utilidades de sistema que tu aplicación necesita para funcionar.
- `Metadatos`: Información sobre la imagen, como los puertos que expone, los volúmenes que utiliza, el comando por defecto para ejecutar al iniciar un contenedor, y las variables de entorno.

> [!NOTE]
> **Las imágenes son inmutables**; una vez que se crea una imagen, no puede ser modificada. Si se necesita cambiar algo, entonces, se debe de crear una nueva imagen.

<a id="dockerfiles"></a>
### Dockerfiles
Un Dockerfile **es un script de texto que contiene una serie de instrucciones y argumentos para construir una imagen de Docker**. Cuando se ejecuta el comando docker build y se le está apuntado a un Dockerfile, Docker lee las instrucciones de arriba hacia abajo y las ejecuta en secuencia para crear la imagen.

> Ejemplo
```
# Dockerfile

# Usa una imagen base de Node.js en la versión 18
FROM node:18-alpine

# Establece el directorio de trabajo dentro del contenedor
WORKDIR /app

# Copia los archivos de configuración de dependencias primero
COPY package*.json ./

# Instala las dependencias del proyecto
RUN npm install

# Copia el resto del código de la aplicación
COPY . .

# Expone el puerto 3000 del contenedor
EXPOSE 3000

# Comando para ejecutar la aplicación cuando el contenedor se inicia
CMD ["npm", "start"]
```

> Instrucciones comunes en Dockerfiles

1. `FROM`: Define la imagen base sobre la cual se construirá la nueva imagen. Es la primera instrucción y obligatoria
2. `LABEL`: Añade metadatos a una imagen (autor, versión, etc.)
3. `WORKDIR`: Establece el directorio de trabajo para las siguientes instrucciones RUN, CMD, ENTRYPOINT, COPY, ADD
4. `COPY`: Copia archivos o directorios desde el host al sistema de archivos de la imagen
5. `ADD`: Similar a COPY, pero con funciones adicionales como la extracción automática de archivos tar o el manejo de URLs remotas
6. `RUN`: Ejecuta un comando en una nueva capa sobre la imagen actual y "commitea" el resultado. Utilizado para instalar paquetes, compilar código, etc
7. `ENV`: Establece variables de entorno que estarán disponibles dentro del contenedor.
8. `ARG`: Define variables que se pueden pasar en tiempo de construcción del Dockerfile (con docker build --build-arg)
9. `EXPOSE`: Informa a Docker que el contenedor escuchará en los puertos de red especificados en tiempo de ejecución. No publica el puerto automáticamente.
10. `CMD`: Proporciona comandos predeterminados o argumentos para un contenedor en ejecución. **Solo puede haber un CMD por Dockerfile; si se especifica más de uno, solo el último tendrá efecto**
11. `VOLUME`: Crea un punto de montaje para volúmenes de datos persistentes.

<a id="imagen-base-vs-derivada"></a>
#### Imágen base vs Imágen derivada

> Imagen Base (Base Image)

* Es la imagen que sirve como punto de partida para el Dockerfile y es especificada por la instrucción FROM.
* `Contiene un sistema operativo mínimo (como Alpine, Ubuntu, Debian slim)` y, a menudo, un entorno de ejecución específico `(como node, python, openjdk, golang)`.
* Son imágenes que no tienen una instrucción FROM propia; su primera instrucción es generalmente la capa FROM scratch (una imagen completamente vacía) o son construidas directamente a partir de un sistema de archivos raíz de Linux.

> Imagen Derivada (Derived Image):

* Es la imagen que construyes a partir de una imagen base, añadiéndole tus aplicaciones, configuraciones y dependencias adicionales.
* `Es el resultado de ejecutar docker build sobre un Dockerfile` que comienza con una instrucción FROM apuntando a una imagen base.
* `Ejemplos`: Una imagen que contiene tu aplicación web de Node.js construida sobre node:18-alpine, o una imagen con tu API de Python sobre python:3.9-slim.

En resumen, la imagen derivada "hereda" las capas de la imagen base y añade sus propias capas encima.

<a id="multistage-builds"></a>
#### Multistage builds (Construcciones Multi-Etapa)

Las Multistage Builds son una característica avanzada de Dockerfiles introducida para abordar el problema de las "imágenes gordas" (fat images). Antes de las construcciones multi-etapa, a menudo se necesitaba incluir todas las herramientas de compilación, bibliotecas de desarrollo y dependencias transitorias en tu imagen final, lo que resultaba en imágenes de gran tamaño que eran lentas de construir, descargar y tenían una superficie de ataque de seguridad más amplia.

Una construcción multi-etapa **permite definir múltiples etapas FROM en un solo Dockerfile**. **Cada FROM comienza una nueva etapa de construcción**. Se puede copiar artefactos de una etapa anterior a una etapa posterior, dejando atrás todas las herramientas de construcción y dependencias intermedias que ya no son necesarias.

> Ejemplo de Multistage Build (aplicación Go)
```
# Dockerfile

# Primera etapa: la etapa de construcción (build stage)
FROM golang:1.20-alpine AS builder

WORKDIR /app

# Copia el código fuente
COPY . .

# Compila la aplicación Go
# La bandera -o especifica el nombre del binario de salida
# La bandera -ldflags "-s -w" reduce el tamaño del binario y quita la información de depuración
RUN CGO_ENABLED=0 go build -o myapp -ldflags "-s -w" .

# Segunda etapa: la etapa final (production stage)
# Utilizamos una imagen muy pequeña (scratch o alpine) para la imagen final
FROM alpine:latest

WORKDIR /root/

# Copia el binario compilado de la etapa 'builder' a la etapa final
COPY --from=builder /app/myapp .

# Define el comando para ejecutar la aplicación
CMD ["./myapp"]
```

- Explicación del ejemplo:

1. FROM golang:1.20-alpine AS builder
  * Inicia la primera etapa, nombrándola builder.
  * Usa una imagen base de Go que contiene todas las herramientas de compilación.
  * Aquí es donde se compila el código.

2. RUN CGO_ENABLED=0 go build -o myapp -ldflags "-s -w" .
  * Este comando compila la aplicación Go, generando un binario ejecutable llamado myapp.
  * CGO_ENABLED=0 es importante para crear un binario estáticamente enlazado que no depende de bibliotecas del sistema operativo.

3. FROM alpine:latest:
  * Inicia la segunda etapa, que será la imagen final.
  * Usa una imagen base extremadamente ligera (Alpine Linux, que es mínima).

4. COPY --from=builder /app/myapp .
  * Esta es la magia del multistage build. Copia el archivo myapp (el binario compilado) desde la etapa llamada builder al directorio de trabajo de la etapa actual.
  * Todo lo demás de la etapa builder (el compilador Go, los archivos de origen, las dependencias de compilación) se descarta, ya que no son necesarios en la imagen final.

<a id="capas-de-imagenes"></a>
### Capas de imágenes

Las Capas de Imágenes son la forma en que Docker organiza y almacena los diferentes componentes de una imagen. Como se mencionó con Union File Systems, **una imagen de Docker no es una entidad monolítica, sino una colección de capas de solo lectura apiladas una encima de la otra**.

> Características clave de las capas:

`Basadas en Instrucciones del Dockerfile`: Cada instrucción en un Dockerfile **(excepto CMD, ENTRYPOINT, LABEL, EXPOSE, USER, VOLUME, WORKDIR, ARG, que solo añaden metadatos o configuran el entorno para las siguientes capas)** crea una nueva capa en la imagen.

- `Compartibles y Reutilizables`:
  * Si múltiples imágenes usan la misma capa base (por ejemplo, ubuntu:latest), esa capa solo se almacena una vez en el disco del host.
  * Durante el proceso docker build, Docker utiliza un cache de capas. Si una instrucción de Dockerfile y su contexto no han cambiado desde la última construcción, Docker reutilizará la capa existente en lugar de ejecutar la instrucción nuevamente. Esto acelera drásticamente las reconstrucciones. 

> [!NOTE]
> Una buena práctica es colocar las instrucciones que cambian con menos frecuencia (como la instalación de un SO base o dependencias del sistema) al principio del Dockerfile, y las instrucciones que cambian con frecuencia (como el código de tu aplicación) al final. Esto maximiza la reutilización de capas en construcciones sucesivas.

<a id="registro-de-contenedores"></a>
### Registro de contenedores

Un Registro de Contenedores (Docker Registry) **es un sistema de almacenamiento y distribución centralizado para imágenes de Docker**. Es esencialmente una biblioteca o repositorio donde las imágenes de Docker se almacenan, organizan y se ponen a disposición para ser descargadas (pulled) por otros usuarios o sistemas.

> Propósito y funcionalidad

 * `Almacenamiento y versión`: Permite almacenar las imágenes de Docker de forma persistente, asociadas a nombres y etiquetas (versiones).
 * `Distribución y colaboración`: Facilita que los desarrolladores compartan sus imágenes entre sí y con los entornos de producción. Es el mecanismo principal para "mover" imágenes entre diferentes hosts Docker.
 * `Seguridad y control de acceso`: Los registros pueden implementar autenticación y autorización para controlar quién puede subir (push) o descargar (pull) imágenes.
 * `Automatización de CI/CD`: Son un componente fundamental en los pipelines de Integración Continua/Despliegue Continuo (CI/CD), donde las imágenes recién construidas se suben al registro y luego se despliegan desde allí.

1. Docker Hub (público):
* Es el registro público predeterminado y más grande, mantenido por Docker Inc
 * Contiene millones de imágenes, incluyendo:
    - Imágenes Oficiales: Son imágenes mantenidas por los proveedores de software (ej., ubuntu, nginx, node, python, mysql). Están bien documentadas, parcheadas regularmente y construidas con las mejores prácticas
    - Imágenes de la Comunidad: Subidas por usuarios individuales y organizaciones. Pueden variar en calidad y seguridad
* Ofrece repositorios públicos y privados (estos últimos suelen requerir una suscripción de pago)
  
2. Registros privados (self-hosted o de proveedores cloud):
* La mayoría de los proveedores de la nube ofrecen sus propios servicios de registro de contenedores gestionados:
    - Amazon Elastic Container Registry (ECR)
    - Google Container Registry (GCR) / Google Artifact Registry
    - Azure Container Registry (ACR)
    - Red Hat Quay.io

Estos servicios ofrecen alta disponibilidad, escalabilidad, seguridad, integración con otros servicios de la nube y, a menudo, funciones adicionales como escaneo de vulnerabilidades.

> Operaciones Comunes con un Registro:

  * `docker login`: Autenticarse con un registro (ej., Docker Hub).
  * `docker pull <nombre_imagen>[:tag]`: Descargar una imagen desde un registro.
  * `docker push <nombre_imagen>[:tag]`: Subir una imagen a un registro. Requiere que la imagen esté etiquetada con el nombre del registro (ej., myregistry.com/myuser/myimage:v1.0).

<a id="etiquetado-de-imagenes"></a>
### Etiquetado de imágenes

El Etiquetado de Imágenes es el proceso de asignar una o más "etiquetas" (tags) a una imagen de Docker. Una etiqueta es una cadena de texto (a menudo una versión, como v1.0, latest, dev, un hash de commit, o una fecha) que **se utiliza para identificar una versión específica de una imagen en un repositorio**.

> Propósito y mejores prácticas:

1. Versionado y control:
  * `Despliegues controlados`: Saber qué versión exacta de una aplicación está desplegada en producción, pruebas o desarrollo.
  * `Rollbacks`: Si una nueva versión tiene un problema, puedes volver fácilmente a una versión anterior estable usando su etiqueta.
  * `Auditoría`: Rastrear el historial de cambios de una imagen.

2. latest Tag:
  * La etiqueta latest es una convención, no una garantía de que sea la última versión. **Es el valor predeterminado si no especificas una etiqueta**.
  * Depender excesivamente de latest puede ser problemático en producción porque la imagen a la que apunta latest puede cambiar inesperadamente, llevando a entornos inconsistentes. Es preferible usar etiquetas explícitas y fijas (ej., 1.0.0, 1.0.1, 20250526-abcde).

3. Múltiples Etiquetas: 
  * Una misma imagen (identificada por su hash de contenido) puede tener múltiples etiquetas. Por ejemplo, **una nueva versión de tu aplicación podría tener v1.2.0 y también latest** (hasta que salga la siguiente versión)
  * Comandos como docker tag <ID_de_imagen> <nuevo_nombre_completo_con_etiqueta> permiten añadir etiquetas a una imagen existente

<a id="ciclo-de-vida-de-un-contenedor"></a>
### Ciclo de vida de un contenedor
El ciclo de vida de un contenedor Docker se refiere a las diferentes etapas por las que pasa un contenedor, desde su creación hasta su eliminación.

1. Creación (Created)
  * `Comando`: docker create <imagen>
  * `Descripción`: Esta etapa crea un nuevo contenedor a partir de una imagen. En este punto, el contenedor existe en el disco con un sistema de archivos y una configuración inicial, pero aún no está en ejecución. Docker le asigna un ID único y un nombre (si no se especifica uno, Docker genera uno aleatorio).

2. Arranque/Inicio (Running / Up)
  * `Comando`: docker start <nombre_o_id_contenedor> o docker run <imagen> (que es create + start).
  * `Descripción`: El contenedor pasa del estado Created o Exited al estado Running. El proceso principal del contenedor se inicia (el comando especificado en CMD o ENTRYPOINT del Dockerfile), y el contenedor comienza a ejecutar su aplicación. Los recursos de red y almacenamiento se activan.

3. Pausa (Paused)
  * `Comando`: docker pause <nombre_o_id_contenedor>
  * `Descripción`: Suspende todos los procesos dentro de un contenedor en ejecución. El contenedor permanece en memoria, pero sus procesos se detienen temporalmente. Esto puede ser útil para depuración o para liberar momentáneamente recursos de CPU sin detener el contenedor por completo.

4. `Reanudar`: docker unpause <nombre_o_id_contenedor> vuelve a Running.

5. Detención (Stopped / Exited)
  * `Comando`: docker stop <nombre_o_id_contenedor>
  * `Descripción`: El contenedor se detiene, enviando una señal SIGTERM (por defecto, para un apagado gracioso) al proceso principal dentro del contenedor. Si el proceso no termina después de un tiempo de espera (por defecto 10 segundos), Docker envía una señal SIGKILL para forzar su terminación. Los recursos de CPU y memoria se liberan, pero el estado del contenedor (sistema de archivos de la capa de escritura y metadatos) persiste en el disco.

6. Reinicio (Restarting):
  * `Comando`: docker restart <nombre_o_id_contenedor>
  * `Descripción`: Detiene un contenedor en ejecución y lo vuelve a iniciar. Es útil cuando necesitas aplicar cambios de configuración o resolver problemas temporales sin eliminar el contenedor.
    
7. Eliminación (Deleted):
  * `Comando`: docker rm <nombre_o_id_contenedor>
  * `Descripción`: Elimina el contenedor del disco. Se borran la capa de escritura del contenedor y todos sus metadatos asociados. Importante: Un contenedor debe estar en estado Exited (detenido) para poder ser eliminado. Si está Running, primero debes detenerlo (docker stop) o forzar la eliminación (docker rm -f).


<a id="redes-en-docker"></a>
## Redes en Docker
Docker, por defecto, **crea y gestiona redes virtuales que permiten a los contenedores comunicarse de forma aislada y segura**. Estas redes se construyen utilizando las capacidades de namespaces de red del kernel de Linux, lo que proporciona a cada contenedor su propia pila de red independiente.

<a id="tipos-de-redes)"></a>
### Tipos de redes en Docker

1. Red bridge (por defecto):

  * `Descripción`: Es la red predeterminada cuando se instala Docker. Si no especificas una red al ejecutar un contenedor, se conectará a la red bridge por defecto.
  * `Funcionamiento`: Docker crea un dispositivo de red virtual de tipo bridge (puente) en el host (comúnmente llamado docker0). Cada contenedor conectado a esta red obtiene su propia interfaz de red virtual que se conecta a este puente. **El puente docker0 actúa como un enrutador virtual, permitiendo la comunicación entre los contenedores conectados a él**.
  * `Consideraciones`: Es adecuada para contenedores aislados o para un grupo pequeño de contenedores que necesitan comunicarse entre sí en un mismo host. Sin embargo, para aplicaciones multi-servicio que necesitan comunicación por nombre de servicio, o para comunicación entre hosts, no es la opción ideal.

2. Redes definidas por el usuario (User-defined Bridge Networks):

  * `Descripción`: Son redes bridge personalizadas que **se crean explícitamente con docker network create**. Estas son las redes recomendadas para aplicaciones multi-servicio en un solo host.
  * `Funcionamiento`: Son similares a la red bridge por defecto, pero con una diferencia crucial: **proporcionan resolución DNS automática entre contenedores**.
  * `Comunicación`: Los contenedores conectados a la misma red definida por el usuario **pueden comunicarse entre sí utilizando sus nombres de contenedor** (o nombres de servicio en Docker Compose) como nombres de host, gracias a un servidor DNS integrado de Docker.
  * `Ventajas`:
      - `Mejor aislamiento`: Proporcionan un mejor aislamiento que la red bridge por defecto, ya que solo los contenedores conectados a esa red pueden verse entre sí.
      - `Descubrimiento de servicios`: Facilitan la comunicación entre microservicios sin necesidad de conocer las IPs dinámicas.

3. Red overlay (para Docker Swarm y Kubernetes):

  * `Descripción`: Estas redes están diseñadas para permitir la comunicación entre contenedores que se ejecutan en diferentes Docker Hosts en un clúster. Son fundamentales para la orquestación.
  * `Funcionamiento`: El driver overlay utiliza tecnologías como VxLAN para crear una red virtual distribuida que se extiende por múltiples hosts. Cada host en el clúster puede ver los contenedores de otros hosts como si estuvieran en la misma red local.
  * `Casos de uso`: Es el tipo de red utilizado por Docker Swarm para la comunicación entre servicios y también por Kubernetes para su modelo de red.

<a id="publicacion-de-puertos"></a>
### Publicación de puertos
La publicación de puertos (también conocida como port forwarding o port mapping) **es el mecanismo por el cual los puertos de un contenedor en ejecución se mapean a puertos en el host de Docker**. Esto permite que los servicios que se ejecutan dentro del contenedor sean accesibles desde fuera del host de Docker (ya sea desde la red local del host o desde Internet)

¿Por qué es necesario?
Por defecto, los contenedores están aislados en su propia red. **Si se ejecuta un servidor web en el puerto 80 dentro de un contenedor, ese puerto solo es accesible desde dentro de la red Docker** a la que está conectado el contenedor. Para que el navegador o cualquier cliente externo pueda acceder a ese servidor web, se necesita "publicar" el puerto del contenedor en el host.

El argumento -p o --publish se utiliza para publicar puertos. Su sintaxis general es:
  * `docker run -p <puerto_del_host>:<puerto_del_contenedor> <imagen>`

`<puerto_del_host>`: El puerto en el sistema operativo del host **donde deseas que el servicio del contenedor sea accesible**.
`<puerto_del_contenedor>`: El puerto en el que el servicio dentro del contenedor está escuchando.

> EXPOSE vs. PUBLISH (-p):

- `EXPOSE (en Dockerfile)`: Es solo una instrucción de metadatos en el Dockerfile que documenta qué puertos espera exponer la aplicación dentro del contenedor. **No publica el puerto automáticamente. Sirve como una pista para el operador del contenedor**.
- `PUBLISH (-p en docker run)`: Es el comando **real que realiza el mapeo del puerto del contenedor al puerto del host**, haciendo que el servicio sea accesible desde el exterior.

<a id="dns-y-descubrimiento-de-servicios"></a>
### DNS y descubrimiento de servicios

El descubrimiento de servicios es la capacidad de una aplicación para encontrar y comunicarse con otras aplicaciones o servicios en una red. En el contexto de Docker, esto se refiere a cómo los contenedores pueden localizarse y conectarse entre sí.

1. Resolución DNS en redes bridge definidas por el usuario:
Los contenedores conectados a esta red pueden comunicarse entre sí utilizando sus nombres de contenedor como nombres de host. Docker resuelve automáticamente estos nombres de host a las direcciones IP internas de los contenedores.
```
  docker network create my-app-network
  docker run -d --name db-container --network my-app-network postgres:latest
  docker run -d --name app-container --network my-app-network my-web-app:latest
```
Dentro de app-container, puedes conectarte a la base de datos usando el nombre de host db-container, y Docker se encargará de resolverlo a la IP correcta.

2. Docker Compose y Descubrimiento de Servicios:

Docker Compose hace un uso extensivo de las redes definidas por el usuario. Cuando se definen múltiples servicios en un archivo docker-compose.yml, **Compose automáticamente crea una red para esos servicios**. Dentro de esa red, los servicios pueden comunicarse entre sí usando sus nombres de servicio (tal como los defines en el docker-compose.yml) como nombres de host.

```
# docker-compose.yml
version: '3.8'
services:
  web:
    image: my-web-app:latest
    ports:
      - "80:80"
  db:
    image: postgres:latest
    environment:
      POSTGRES_DB: mydatabase
```
En este ejemplo, la aplicación web podría conectarse a la base de datos usando el nombre de host db, y Compose se encargaría de la resolución DNS.

<a id="almacenamiento-en-docker"></a>
## Almacenamiento en Docker

El sistema de archivos de un contenedor es efímero. Cuando un contenedor se detiene y se elimina, todos los cambios realizados en su capa de escritura se pierden. Esto es intencional, ya que promueve la filosofía de infraestructura inmutable y la facilidad de reemplazo de contenedores. Sin embargo, la mayoría de las aplicaciones necesitan almacenar datos de forma persistente (bases de datos, logs, configuraciones de usuario, etc.).

Docker proporciona varias opciones para persistir datos y montarlos en contenedores, cada una con sus propios casos de uso y características. 

<a id="volumes"></a>
### Volúmenes (volumes)
Los volúmenes son el mecanismo preferido y **recomendado por Docker para persistir datos generados y utilizados por los contenedores**.

Un volumen existe independientemente del ciclo de vida de cualquier contenedor. Puedes detener y eliminar un contenedor, y el volumen y sus datos permanecerán intactos, listos para ser utilizados por un nuevo contenedor.

> Volúmenes Nombrados (Named Volumes)
  * `Creación`: Se crean explícitamente con un nombre (ej. docker volume create my-data).
  *  `Uso`: Se montan en un contenedor especificando su nombre.

> Ejemplo 
```
# Crear un volumen nombrado
docker volume create my-db-data

# Usar el volumen en un contenedor PostgreSQL
docker run -d --name my-postgres \
    -e POSTGRES_PASSWORD=mysecretpassword \
    -v my-db-data:/var/lib/postgresql/data \
    postgres:latest
```
  
En este ejemplo, my-db-data es el nombre del volumen, y se monta dentro del contenedor en la ruta /var/lib/postgresql/data. Todos los datos de PostgreSQL se escribirán en este volumen persistente.


<a id="backups-en-contenedores"></a>
### Backups de datos de contenedores
Dado que los contenedores son efímeros, la persistencia de datos a través de volúmenes es solo el primer paso. Para **garantizar la seguridad y la recuperación de datos, es fundamental tener una estrategia de backups**.

<a id="mount-bindings"></a>
### Mount Bindings
Los Bind Mounts (Montajes de Enlace) son el mecanismo de almacenamiento más antiguo en Docker y **ofrecen una forma de montar un archivo o directorio existente del sistema de archivos del host directamente dentro de un contenedor**.

A diferencia de los volúmenes, los bind mounts **no son gestionados por Docker. Uno mismo es responsable de la ubicación del archivo o directorio en el host**.

<a id="tmpfs-mounts"></a>
### tmpfs Mounts

<a id="drivers-de-almacenamiento"></a>
### Drivers de almacenamiento
