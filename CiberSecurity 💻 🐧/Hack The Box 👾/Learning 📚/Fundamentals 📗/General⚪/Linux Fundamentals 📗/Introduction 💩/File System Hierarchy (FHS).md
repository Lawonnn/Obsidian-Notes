3.0
# Conceptos Básicos:
## Clasificación de directorios:
- **Shareable vs Unshareable**: Los directorios shareable contienen ficheros que pueden ser compartidos entre varios usuarios del sistema, mientras que los unshareable no
- **Static vs Variable**: Los directorios static son aquellos que contienen ficheros cuya información no cambia sin la intervención del administrador del sistema. Son por ejemplo, los binarios, las librerías, documentos, etc. A diferencia de los variable, que cambian a medida del tiempo, los static pueden ser almacenados de forma `read-only` y no necesitan copias de seguridad con la misma frecuencia que los variable
![[FHS-Shareable-Statics.png]]
* ____
# Root File System (/):
El root (/) se compone mayormente por directorios que son utilizados para el boot, restore, recovery y/o repair del sistema

La mayoría de los directorios de root son unshareable los sistemas y usuarios de red
Los requisitos mínimos del sistema de ficheros root deben permanecer lo mas reducido posible, ya que de esta manera hay menos probabilidades de incompatibilidades entre los servicios o programas instalados y un mejor manejo de errores.

Las Aplicaciones nunca deben crear o requerir directorios especiales del sistema de directorios root o subdirectorios dentro de él

### History: 
Se remonta al año 1977 en el que
## Requerimientos:
El root (/) contiene los siguientes directorios que deben estar si o si en él:
![[Directorios del root.png]]

## /bin: Essential user command binaries (for use by all users):
### Propósito:
Contiene comandos que pueden ser usados por el administrador del sistema y cualquier usuario
### Requerimientos:
En el /bin se deben de encontrar los siguientes comando dentro de él:
![[-bin Commands 1.png]]![[-bin Commands 2.png]]
![[-bin Commands 3.png]]

## /boot: Static File of the boot loader:
En este directorio se encuentran todos los fichero/subdirectorios necesarios para el arranque del sistema, excepto ficheros de configuración (configuración de red, de usuarios, entre otros) y el map installer.
En este directorio, se encuentra el kernel del sistema
e


## /dev: Device Files:
Aquí se ubican los los ficheros especiales (que no ocupan espacio en el SSD, pq son herramientas del SO, pero no cuentan con hardware físico detrás, por ejemplo el /dev/null) y de dispositivos.
Si es necesario configurar manualmente algún dispositivo, se usa el comando `udev` o su versión ambigua `makedev`, que se usan para el gestionamiento de dispositivos.

x
## /etc: Host-specifics system configuration
Aquí están los ficheros de configuración que afectan a todo el SO. Un "configuration file" es un fichero que se encarga de controlar las operaciones de un programa, dicho fichero tiene que ser estático y claramente no puede ser un binario ejecutable.
Generalmente estos ficheros se encuentra en subdirectorios de `/etc`, y no directamente en el
### Directorios que están aquí:
![[-etc Directories.Files 1.png]]
![[-etc Directories.Files 2.png]]
![[-etc Directories.Files 3.png]]

### /etc/opt: Configuration File for /opt
Aquí se encuentran los ficheros de configuración de las aplicaciones adicionales que están en `/opt` (de "optional")

### /etc/X11: Configuration for the X Window System
Aquí están los ficheros de configuración de X Window System (X11). El X11 es un servidor que forma parte de los componentes que permite que el GUI (Interfaz Grafica de Usuario) exista. Generalmente, dicho GUI esta formado por:
#### 1. El Servidor (X11 / Xorg)
Él es el que tiene la fuerza bruta. Se encarga de:
- Hablar con la tarjeta de video para dibujar píxeles.
- Detectar cuando mueves el ratón o presionas una tecla.
- **Ojo aquí:** X11 solo sabe dibujar "cajas" vacías; no sabe ponerles botones de "cerrar" o bordes bonitos.
#### 2. El Gestor de Ventanas (Window Manager)
Este es un programa que corre sobre X11. Es el que decide:
- Dónde se pone una ventana cuando la abres.
- Ponerle la barra superior con los botones de minimizar, maximizar y cerrar.
- Ejemplos que verás en seguridad: **i3wm**, **Openbox** o el que viene con **XFCE**.
#### 3. El Entorno de Escritorio (Desktop Environment)
Es el paquete completo. Si usas **Kali Linux**, lo más probable es que uses **XFCE** o **GNOME**.
- Esto ya incluye el fondo de pantalla, los iconos, el menú de inicio y las carpetas.

En el /etc/X11, pueden estar los siguientes directorios o ficheros:
![[-etc-X11 Optional Files.png]]

## /home: User home directories (Optional):
Es el directorio de cada usuario del sistema. No hay una especificación en cuanto a la ubicación de los ficheros o directorios dentro de él, ya que puede variar en cada usuario
Los ficheros de configuración específicos del usuario para las aplicaciones se encuentran en un fichero que empieza por un "." dentro del /home de cada usuario. 
Si una aplicación necesita crear mas de un fichero, en vez de ser un fichero que empieza por un ".", sera un directorio que empiece por un ".". En este caso, los configuration files dentro de él no deberían empezar con un "."

## /lib: Essential shared libraries and kernel modules:
Aquí se encuentran las librerías que son utilizadas para bootear el sistema y correr los comandos/binarios del mismo
> **Nota**:
> Los ficheros que terminan en .so (Shared Objects) son aquellos que se utilizan como ficheros de librerías. En Windows, es .dll (Dynamic Link Library)

Uno de los siguientes filenames y directories son requeridos en este directorio:
![[-lib Requirements.png]]
![[-lib Requirements 2.png]]

## /media: Mount point for removable media
Aquí se van a encontrar aquellos dispositivos que nosotros conectemos al sistema, ya sea un pendrive, discos duros externos, etc. Aquí generalmente se alojaran aquellos dispositivos con un uso temporal (conectas el USB, lo usas, lo sacas)

## /mnt: Mount point for temporarily removable mounted filesystem
Es bastante parecido a /media, pero se utiliza cuando se quiere montar algo de forma manual y más permanente. Por ejemplo, si se quiere colocar un disco duro de una víctima para analizarlo, o una partición de red compartida por un largo tiempo, esta aparecerá en /mnt

## /opt: Add-on application software packages
Este directorio esta reservado para las add-on applications (las aplicaciones que añadimos o que no vienen con el sistema)
Generalmente, los subdirectorios dentro de /opt siguen esta sintaxis:
`/opt/<provider>/<package>`
Donde:
`<provider>`: Es el proveedor del cual se descargan sus apps. Por ejemplo si Google descarga algo lo haría: */opt/google/chrome*
`<package>`: Es simplemente el ejecutable/programa
![[-opt sintaxis.png]]

## /run: Run-time variable data
Es un directorio interesante, ya que no se encuentra en el disco duro (SSD), sino que se aloja directamente en la RAM. Por cada vez que apagamos el sistema, este directorio se borra. Aquí, esta la informacion que describe el sistema desde que es booteado
Por ejemplo, aquí se suelen guardar:

- **Archivos PID (Process ID):** Son archivos pequeñitos que solo contienen un número. Si el servidor de bases de datos arranca, escribe su número de proceso en `/run/mysql.pid`. Así, si intentas abrirlo otra vez, el sistema mira ese archivo y dice: "Un momento, ya hay alguien trabajando con este número, no puedo abrirlo de nuevo".
    
- **Sockets:** Son como "enchufes" virtuales. Si un programa A quiere hablar con el programa B sin usar internet, usan un archivo en `/run` como puente.
    
- **Información de usuarios:** Si haces `ls /run/user`, verás una carpeta con un número (probablemente `1000`). Ahí Linux anota qué cosas tienes permiso de hacer tú en esta sesión específica.
Antiguamente, este directorio se alojaba en /var (/var/run). Sin embargo, como el /var reside en el disco duro (SSD), si el sistema tenía un error al arrancar y no podía leer el disco, no podía crear esos archivos críticos y el sistema no terminaba de encender. Por eso, los desarrolladores decidieron separar este directorio y colocarlo directamente en la raíz (/run) y que se alojara en la RAM. Es más rápido, mas seguro y no depende del SSD.
Sin embargo, como algunos programas viejos siguen usando /var/run, se decidió mantener los dos para que no haya ningún problema.

## /sbin: System Binaries
Aquí están los binarios que solo el root y los que tengan permiso en el sudoers pueden ejecutar. Estos binarios son conocidos como *root-only commands* y engloban binarios esenciales para el booting, restoring and recovering/repairing the system