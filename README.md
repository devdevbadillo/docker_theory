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
  - [Optimización de imágenes](#optimizacion-de-imagenes)
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
