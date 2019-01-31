# Práctica 1

## Sesión 1

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

  - **RAID0**: no duplica datos, solo reparte la información entre los diferentes discos para acelerar la lectura/escritura
  - **RAID1**: Crea copias exactas en los diferentes discos para aumentar la
redundancia de datos y por tanto la seguridad
  - **RAID5**: Divide los datos a nivel de bloque para obtener y almacenar la paridad
para detectar y corregir errores. Tiene baja redundancia y necesita 3 discos
como mínimo para poder implementarse.
  - **RAID6**: Funciona igual que el 5 pero genera bloques de paridad duplicados y los distribuye entre todos los discos para una mayor seguridad.
- **MBR**: Mates Boot Record, registro de arranque principal, conocido también.
