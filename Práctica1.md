# Práctica 1

## Sesión 1

### Instalación Ubuntu Server
- Instalar ubunto con sda y sdb RAID1 (MD0).
- `lsblk` para comprobar instalación.

### Apuntes

- **VPS**: Virtual private server
- **LOPD**, **GPDR**: normativas de protección de datos.
- **Serverless**: modo de trabajo en el cual tú no tienes ningún servidor ni propio ni alquilado, sino que tú mandas un código a ejecutar (usando la API del servidor) y el servidor te devuelve el resultado. De esta forma no hay que preocuparse de mantenimiento ni inversión inicial.
- **Contenedor** o **Docker**: un contenedor es una recopilación cerrada de aplicaciones, datos o recursos la cual permite ejecutar una determinada aplicación en cualquier SO permitiendo gran portabilidad.
- **Maquina virtual**: una maquina virtual nos permite simular hardware (cogiéndolo de nuestra maquina) e instalar cualquier SO para hacer uso de el independientemente
del SO anfitrión donde se esta ejecutando la máquina virtual.
- **VMSW**: programa de virtualización (virtual box o VMware), virtual machine software.
- **FDE**: full disk encription.
- **LVM**: administrador de volúmenes lógicos de Linux. Nos permite crear volúmenes
lógicos para ganar flexibilidad y poder redimensionar el espacio sin reiniciar siquiera.
- **RAID**: redundant array of independent disks. Es un sistema de datos con multiples unidades entre las cuales distribuye y replica los datos. Un raid puede estar implementado en hardware o software.

    |   |  HW |  SF |
    |---|:-:|:-:|
    | Expansibilidad  | Menor  | Mayor  |
    | Bug/Virus  | Menor  |  Mayor |
    |  Precio | Mayor  |  Menor |
    |  Eficiencia | Mayor  |  Menor |

  - **RAID0**: no duplica datos, solo reparte la información entre los diferentes discos para acelerar la lectura/escritura.
  - **RAID1**: Crea copias exactas en los diferentes discos para aumentar la
redundancia de datos y por tanto la seguridad.
  - **RAID5**: Divide los datos a nivel de bloque para obtener y almacenar la paridad
para detectar y corregir errores. Tiene baja redundancia y necesita 3 discos
como mínimo para poder implementarse.
  - **RAID6**: Funciona igual que el 5 pero genera bloques de paridad duplicados y los distribuye entre todos los discos para una mayor seguridad.
- **MBR**: Master Boot Record, registro de arranque principal.

---

## Sesión 2

### Instalación CentOS
Ejecutar `df -h` y `lsblk` y comprender la instalación.
pvs -> un volumen fisico que sustenta a un grupo de volúmenes con dos volúmenes lógicos (/ y swap). Instalación por defecto de CentOS.

### Escenario 1
Necesitamos mucho espacio en /var, /var se nos queda pequeño, luego necesitamos añadir un disco.

#### Pasos:
1. Añadir disco en VBox (sdb) crear phisical volume.
```
pvcreate /dev/sdb
```
- cl extender el volume group lógico a sdb.
```
vgextend cl /dev/sdb
```
- crear logical volume.
```
lvcreate -L 4G -n newvar cl
```
- crear sistema de archivos
```
mkfs -t ext4 /dev/cl/newvar
```
formas de hacer referencias a un Logical volume Consultar!!
- hacer disponible dicha información en el sistema de archivos
```
mkdir /media/newvar
mount /dev/cl/newvar /media/newvar
```
- copiar, primero cambiar nivel de ejcucion, con cp . preservamos metadatos y privilegios, y copiamos archivos ocultos, -a para preservar contexto
```
systemctl isolate runlevel1.target
ls -Z
cp -a /var/. /media/newvar
```

- asignar nuevo punto de espacio
```
vi /etc/fstab (/dev/mapper/cl-newvar /var ext4 defaults 0 0)
mount -a
fstab
unmount /media/newvar
```
- liberar espacio, volver a configuracion inicial, mover /var /varold, mkdir /var

```
unmount /dev/mapper/cl-newvar
mv /var /varold
mkdir /var
restorecon /var
mount -a
lsbk
```

### Apuntes

- Diferencia entre cp /var, /var/., y cp/var/*
  - El primero, copia la carpeta en si dejandola en el destino como
destino/var/contenidodevar.
  - El segundo, copia el contenido de var con todos sus archivos ocultos.
  - El tercero, copia el contenido de var con todos sus archivos (salvo los ocultos).
- **SE linux**: Security-Enhanced Linux (SELinux) es un módulo de seguridad para el
kernel Linux que proporciona el mecanismo para soportar políticas de seguridad para
el control de acceso, incluyendo controles de acceso obligatorios como los del
Departamento de Defensa de Estados Unidos.
- Atomicidad al copiar: Las copias de seguridad de servicios en uso deben ser
atómicas ya que un usuario puede escribir mientras se copia corrompiendo la
información copiada. En Linux esto se soluciona cambiando el nivel de ejecución
(monousuario) con el comando: `systemctl isolate runlevel1.tarjet`

- `/etc/fstab`: Fichero donde se indican los puntos de montaje que se van a realizar
siempre que encendamos el servidor.
- `df -h`: Muestra el sistema de ficheros.
- `lsblk`: Visualiza los dispositivos, unidades, particiones y sus capacidades (montadas o no).
- `pvdisplay`, `pvs`: Visualiza los volúmenes físicos.
- `vgdisplay`, `vgs`: Visualiza los grupos de volúmenes lógicos.
- `lvdisplay`, `vgs`: Visualiza los volúmenes lógicos.
- `fdisk`: Con esta herramienta podremos crear, eliminar, redimensionar, cambiar o
copiar y mover particiones usando el sencillo menú que ofrece.
- `mount`: Sirve para montar particiones al sistema de archivos.
- `umount`: Sirve para desmontar particiones del sistema de archivos.
- `pvcreate`: Crea un volumen físico.
- `vgextend`: Crea un grupo de volúmenes lógicos.
- `lvcreate`: Crea un volumen logico
- `2mkfs`: Crea una partición en un dispositivo o volumen lógico.
- `restorecon`: Restaura el contexto
- **vi**:
  - Con `i` entra en modo inserción de texto.
  - Con `esc` entra en modo comando.
  - Con `:wq` Comando para guardar y cerrar el archivo. o q! comando para cerrar sin guardar.

---

## Sesión 3

### Escenario
Instalación de RAID1 y encriptación, no se va a encriptar swap.

#### Creación de RAID1 y montaje de var:
1. Creación de dos discos 2GB. Comprobamos con ```lsblk```.
- Activamos la red mediante `ifup enp0s3`.
- Instalación de ```mdadm``` mediante `yum install mdadm`.
- Editamos la configuración de red para no tener que activarlo cada vez que reiniciemos, archivo: `/etc/sysconfig/network-scripts/ifcfg-enp0s3`.
- Creamos el RAID1 mediante el comando `mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdc /dev/sdd`.
- Creamos el PV mediante `pvcreate /dev/md0`.
- Creamos el VG mediante `vgcreate pmraid1 /dev/md0`.
- Creamos el LV mediante `lvcreate -L 1G -n newvar1 pmraid1`.
- Creamos el filesystem `mkfs -t ext4 /dev/mapper/pmraid1-newvar`
- `mkdir /media/newvar1`
- `mount /dev/mapper/pmraid1-newvar1 media/newvar1/`
- `cp -a /var/. /media/newvar1/`

#### Cifrado:
1. Instalamos `cryptsetup` mediante `yum install cryptsetup`.
- Creamos copia de seguridad `mkdir /varRAID || cp -a /var/ /varRAID/`.
- Instalamos `lsof` mediante `yum install lsof`.
- Mediante `lsof /var` observamos que proceso hace uso de /var.
- Matamos el proceso `kill -9`.
- Desmontamos mediante `unmount /dev/mapper/pmraid1-newvar1`.
- Ciframos mediante `cryptsetup luksFormat /dev/mapper/pmraid1-newvar1`.
- Activamos mediante `cryptsetup luksOpen /dev/mapper/pmraid1-newvar1 pmraid1-newvar_crypt`.
- Creamos sistema de archivos mediante `mkfs -t ext4 /dev/mapper/pmraid1-newvar1_crypt`.
- Montamos .... Copiamos.
- Obtenemos el UUID de pmraid1-newvar1 cifrado mediante `blkid | grep crypto >> /etc/crypttab`
- Editamos el archivo `/etc/crypttab` para añadir el LV especificado en fstab lv_name UUID=..... none.
- Editamos `/etc/fstab`.

## Apuntes

- `mdadm`: multi device administrator, sirve para administrar los raids en un sistema linux.
- `ip addr`: mostrar interfaces de red.
- `lspci`: muestra el hardware en el bus pci.
- `ifup enp0s3`: habilitar interfaz de red enp0s3.
- mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdc /dev/sdd
- cryptsetup luksFormat
- cryptsetup luksOpen
- shred del LV para evitar volcado y cajanegra
- `lsof <recurso>`: muestra quien mantiene al recurso ocupado.
- `blkid`: Obtener tipo de partición, UUID y el identificador del nodo.
- LVM on LUKS --> usado en ubuntu
- LUKS on LVM
- **UUID**: identificador universal único.

---
