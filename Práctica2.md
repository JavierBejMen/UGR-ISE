# Práctica2

## Sesión 1

Instalación Ubuntu:
- Instalamos open-ssh `sudo apt install openssh-server`.
- `ps -Af | grep ssh` para ver que está ejecuntandose después de la instalación.
- `systemctl status sshd.service` para monitorizar el daemon sshd. Ubuntu permite ssh.service o (ssh, sshd), pero según Alberto no es correcto.

Instalación CentOS:
- `yum search ssh | grep server` para buscar el paquete. Ya está instalado por defecto.
- `systemctl status sshd`, CentOS no permite la ambiguedad ssh/sshd.

Configuración servicio SSH Ubuntu:
- Abrimos el archivo de configuración para edición `sudo vi /etc/ssh/sshd_config` y cambiamos la opción `Port 22` por `Port 22022`.
- Reiniciamos el servicio `sudo systemctl restart sshd.service`
- Para comprobar que funciona, desde el host `ssh dir.ip -p 22022 -l <user>`. O desde la MV `ssh localhost`.
- Ahora cambiamos el archivo de configuración para no permitir loguearse al usuario root, `sudo vi /etc/ssh/sshd_config` ---> cambiamos `PermitRootLogin prohibit-password` por `PermitRootLogin no`.
- Reiniciamos, `sudo systemctl restart sshd`.

Acceso sin contraseña Ubuntu y CentOS:
- Generamos el par de claves, `ssh-keygen` ---> elegir archivo ---> paraphrase ---> again.
- `ssh-copy-id <user><ip.dir>:22022`
- `sudo vi /etc/ssh/sshd_config` ---> `PasswordAuthentication no`

Configurar servicio SSH CentOS:
- Mediante el comando sed cambiamos el puerto por defecto:
```
sudo sed -e "s/#Port 22/Port 22022/" -i /etc/ssh/sshd_config
```
- Reiniciamos el servicio `sudo systemctl restart sshd.service`.
- Nos da un error comprobamos que ha fallado, `sudo systemctl status sshd` o `journalctl -xe`.
- Vamos a indicarle a SELinux para que añada el puerto 22022, instalamos semanage, para saber que paquete utilizamos la opcion provide, `yum provides semanage`, luego, `yum install policycoreutils-python`.
- Abilitamos el puerto, `sudo semanage port -a -t ssh_port_t -p tcp 22022`.
- Abrimos el puerto en el firewall, `sudo firewall-cmd --permanent --add-port=22022/tcp`
- Recargamos el firewall `sudo firewall-cmd --reload`


### Comandos
- tasksel
- systemctl
- sed
- journalctl
- ufw (ubuntu)
- firewall-cmd (CentOS)

### Apuntes
- Gestor de paquetes 2ª generación: snap (Ubuntu18) y dnf(fedora).
- SSH (Secure Shell) lo desarrolló la escuela de computación de alto en findlandia, Spot.
- SSH es un servicio, está escuchando en un puerto concreto (22/tcp).

|   |  Instalada |  Nomenclatura | Arch. Conf. | Firewall | Act. FW |
|---|---|---|---|---|---|---|---|
|Ubuntu| No  |  ssh/sshd | Sin comentar| ufw | No |
|CentOS|  Si |   ssh| Comentadas por defecto | firewall-cmd | Si|

------------------------
