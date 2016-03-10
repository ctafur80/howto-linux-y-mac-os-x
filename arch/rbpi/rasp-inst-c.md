## Instalación complementaria

La instalación complementaria y de los drivers de la tarjeta wifi está basada en [este
vídeo](www.youtube.com/watch?v=kCb5rmFJwzs) y en las entradas
[fstab](https://wiki.archlinux.org/index.php/Fstab#Automount_with_systemd), [Persistent block device
naming](https://wiki.archlinux.org/index.php/Persistent_block_device_naming) y
[Autofs](https://wiki.archlinux.org/index.php/Autofs) del wiki de Arch Linux.

Aquí suponemos que ha accedido a su sistema RbPi por una sesión remota de SSH mediante la interfaz Ethernet (que
lo más probable es que el sistema use el designador `eth0` para referirse a ella). La cuenta a la que deberá
acceder es a root, con contraseña root, si no la ha cambiado. Deberá conocer la dirección MAC de la interfaz
eth0 del RbPi. Si no la conoce, puede entrar en la configuración de su router y verla ahí. Una vez que la
conozca, es aconsejable configurar el router para que asigne siempre una misma dirección IP a dicha dirección
MAC. Así accederá por SSH a la misma dirección siempre; de lo contrario, tendría que buscar qué dirección IP
tiene asignada su interfaz eth0 tras cada reinicio. También puede configurar un nombre, para acceder sin
necesidad de escribir la dirección, en su fichero /etc/hosts (en el del ordenador desde el que establecerá la
sesión remota por SSH con el RbPi).

Otro inconveniente para que pueda acceder por sesión de SSH es que, tras la instalación básica, la única cuenta
que tiene en su sistema es root y viene deshabilitada la opción de acceder por SSH a la cuenta root del sistema.
Así, pues, para habilitarla deberá editar /etc/ssh/sshd_config y, si tiene descomentado

```
PermitRootLogin without-password
```

cámbielo por

```
PermitRootLogin yes
```

Puede hacerlo también, si no tiene un teclado y pantalla donde conectarlo, montando la tarjeta microSD en su
sistema.

Luego puede, bien reiniciar el sistema, o bien reiniciar el servidor SSH:

```
#? systemctl restart sshd.service
```

Lo primero que suelo hacer es copiar los ficheros de configuración de mi ordenador, en el directorio
/home/miUsuario/Dropbox/Docuentos/rasp/rbpiHostname/:

```
#? scp -r /home/miUsuario/Dropbox/Documentos/rasp/ root@rbpiHostname-en0:/root/
```

Ahora actualice el RbPi (vea subsec:arch-update). Una vez terminada la actualización, puede instalar
los paquetes que siempre suele usar:

```
#? pacman -S vim vim-spell-en vim-spell-es vim-colorsamplerpack wpa_supplicant bash-completion sudo wget
```

Ahora puede introducir desde su RbPi el siguiente comando

```
#? cat /root/rasp/bashrc-add >> /etc/bash.bashrc
#? rm /root/rasp/bashrc-add
```

para añadir a /etc/bash.bashrc la configuración a mi gusto. También puede hacer lo mismo con el
fichero de configuración global de Vim:

```
#? cat /root/rasp/vimrc-add >> /etc/vimrc
#? rm /root/rasp/vimrc-add
```

Ahora deberá reiniciar su RbPi (vea subsec:arch-reboot). También puede, luego, si lo desea, eliminar
los paquetes huérfanos que pueda tener, aunque es raro que tenga alguno justo tras instalar el sistema (vea
sec:arch-rm-orphan-p).

Luego, cambie los nombres de host (vea subsec:arch-hostnames). Como puede que haya comprobado, no se le permite
usar acentos gráficos ahora mismo; no es que no lo permita el comando `hostnamectl`, sino que su shell aún no
tiene configurada la entrada de teclado en español (vea subsec:arch-lang). También puede ahora configurar la
fecha y la hora de su sistema; vea subsec:arch-timedate. Ahora reinicie el sistema y comprobará que el prompt
del shell mostrará el nuevo static hostname; ya no se llamará alarmpi, sino hostname.

Ahora debería cambiar el idioma a su gusto. Para ello, introduzca lo siguiente:

```
#? mv /root/rasp/locales/zfur_e{n,s}_* /usr/share/i18n/locales/
```

También deberá mover lo siguiente:

```
#? mv /root/rasp/keymap/zfur_es.map.gz /usr/share/kbd/keymaps/i386/qwerty/
```

Ahora edite /etc/locale.gen y descomente los locales que desea generar (es decir, elimine el `#` a su
izquierda); en mi caso:

```
en_US.ISO-88591-1
en_US.UTF-8 UTF-8
es_ES.ISO-88591-1
es_ES.UTF-8 UTF-8
zfur_en_US.UTF-8 UTF-8
zfur_es_ES.UTF-8 UTF-8
```

Las entradas `zfur_es_ES.UTF-8 UTF-8` y `zfur_en_US.UTF-8 UTF-8` no se han descomentado, sino que he tenido que
escribirlas, como es lógico. Ahora podrá generar los locales. Para ello, introduzca:

```
#? locale-gen
```

Ojo, debe usar siempre este comando para generar los locales nuevos; no vale con reiniciar.

Luego deberá establecer los locales, pero no como lo hizo antes, ahora deberá seleccionar el mapeado de teclas
zfur_es, en lugar de es, tanto en el mapeado de teclas de la consola virtual (VC Keymap), como en el de la
disposición en X11 (X11 Layout):

```
#? localectl set-locale LANG=en_US.utf8 LANGUAGE=en_US \
> LC_NUMERIC=zfur_es_ES.utf8 LC_TIME=es_ES.utf8 LC_MONETARY=zfur_es_ES.utf8 \
> LC_PAPER=es_ES.utf8 LC_NAME=es_ES.utf8 LC_ADDRESS=es_ES.utf8 \
> LC_TELEPHONE=es_ES.utf8 LC_MEASUREMENT=es_ES.utf8 LC_IDENTIFICATION=es_ES.utf8
#? localectl set-keymap zfur_es
#? localectl set-x11-keymap zfur_es
```

Debe ejecutar dos veces el comando para que VC Keymap acepte los el valor que desea usted. Deberá comprobar que
ha realizado correctamente los cambios con el siguiente comando:

```
$ localectl status
System Locale: LANG=en_US.utf8
               LANGUAGE=en_US
               LC_NUMERIC=zfur_es_ES.utf8
               LC_TIME=es_ES.utf8
               LC_MONETARY=zfur_es_ES.utf8
               LC_PAPER=es_ES.utf8
               LC_NAME=es_ES.utf8
               LC_ADDRESS=es_ES.utf8
               LC_TELEPHONE=es_ES.utf8
               LC_MEASUREMENT=es_ES.utf8
               LC_IDENTIFICATION=es_ES.utf8
    VC Keymap: zfur_es
   X11 Layout: zfur_es
```

Reinicie ahora su RbPi (vea TKTK).

También puede sustituir ahora su fichero /etc/fstab por uno a su gusto:

```
#? mv /root/fstab /etc/fstab
```

Con el comando `lsblk` puede ver los nombres descriptores del kernel de las particiones de los dispositivos de
almacenamiento de su sistema:

```
$ lsblk
NAME         MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
mmcblk0      179:0    0 28.8G  0 disk
|- mmcblk0p1 179:1    0  100M  0 part /boot
|- mmcblk0p2 179:2    0    8G  0 part /
`- mmcblk0p3 179:3    0 20.7G  0 part
```

Esto es sólo un ejemplo; no tiene que coincidir con lo que le aparezca a usted. Ahora debe asignarle un sistema
de ficheros a la partición que reservó para datos (si es que no lo hizo ya):

```
#? mkfs.ext4 /dev/mmcblk0p3
```

Si desea que, por ejemplo, el uso de la partición sea el de guardar copias de seguridad, cree primero un
directorio llamado backups en el directorio personal del usuario (/home/username/); es aconsejable que lo haga
con el usuario que ha creado antes, pues así será éste el propietario de dicho directorio y el grupo al que
pertenezca será users; si no lo hace así, tendrá que cambiarlo luego. Ahora introduzca:

```
#? mount /dev/mmcblk0p3 /home/username/backups
```

Si desea que sea, por ejemplo, para descargar ficheros por BitTorrent, lo puede llamar downloads. No es del todo
necesario que introduzca este último comando. Simplemente puede esperar a que se monte tras el reinicio que haga
después de modificar /etc/fstab.

Si quiere que se monte automáticamente así siempre durante el arranque del sistema, deberá editar el fichero
/etc/fstab y hacer que quede algo similar a esto:

```bash
# <filesystem>    <dir>   <type>  <options>   <dump>  <pass>
  /dev/mmcblk0p1  /boot   vfat    defaults    0       0
  /dev/mmcblk0p2  /       ext4    defaults    0       0
  /dev/mmcblk0p3  /home   ext4    defaults    0       2
```

También puede hacer que /etc/fstab tenga en la columna de la izquierda los identificadores únicos de los
dispositivos, es decir, los UUIDs. Según dicen en la wiki de Arch Linux, es mejor. Ubuntu lo hace sin necesidad
de tener que configurarlo. Para ello, primero vea los UUIDs de su sistema:

```
$ lsblk -f
NAME        FSTYPE LABEL UUID                                 MOUNTPOINT
mmcblk0
  mmcblk0p1 vfat         7E6C-040E                            /boot
  mmcblk0p2 ext4         c4779d91-211c-496f-9ce3-04eacd9c9592 /
  mmcblk0p3 ext4         d4e6f401-ef9e-4e61-998f-db45048978c8 /home
```

Éstos son los que aparecen en una de mis RbPi; en la suya deberán ser distintos los valores. Ahora, edite
/etc/fstab y cambie los nombres descriptores del kernel por sus correspondientes UUIDs, sin olvidar poner
`UUID=` a la izquierda, donde antes ponía `/dev/`. En mi ejemplo, sería

```bash
#
# /etc/fstab: static file system information
#
# <file system>                               <dir>   <type>  <options> <dump>  <pass>
  UUID=7E6C-040E                              /boot   vfat    defaults  0       0
  UUID=c4779d91-211c-496f-9ce3-04eacd9c9592   /       ext4    defaults  0       0
  UUID=d4e6f401-ef9e-4e61-998f-db45048978c8   /home   ext4    defaults  0       2
```

Ahora, deberá guardar el fichero, salir y reiniciar el sistema. Si no ha metido la pata, el sistema debería
reiniciar correctamente. Algo que he advertido al hacer esto es que se crea un servicio nuevo de fsck (chequeo)
de la unidad que tiene pass con valor 2. Otra cosa que sucede es que el directorio /home/username/backups/ (o el
que haya creado), tras este reinicio, se ha vuelto propiedad de root y del grupo root. Si lo cambia, ya no
volverá a dar este problema.

TKTKTKTKTK Ahora debe configurar el automontado de sistemas de ficheros. Deberá instalar primero el pquete
Autofs:

man -S autofs ------------- Creo que se puede también con systemd. Sería cuestión de dar la opción automount a
la entrada correspondiente

TKTKTKTKTKTKTKTKTK

Si va a instalar algún entorno de escritorio en su sistema, no tiene que seguir los pasos que se explican aquí,
pues la instalación de dicho entorno lo hará por usted. Sin embargo, si desea no tener un entorno de escritorio
porque desea usar su equipo para un propósito muy específico y no desea que consuma más recursos de los justos y
necesarios, tendrá que seguir las explicaciones que se dan
[aquí](https://wiki.archlinux.org/index.php/USB\_storage\_devices). En mi caso, prefiero que los equipos que uso
para un propósito muy específico no tengan configurado el montaje de dispositivos de almacenamiento por USB; en
caso de que lo desee en algún caso concreto, haré manualmente el montado. Otra cosa es configurar el montado de
alguna unidad por NFS o CIFS. Para esto, puede ver [ésto](https://wiki.archlinux.org/index.php/NFS).

Creo que ahora sería un buen momento de cambiar la contraseña de root. Para ello, deberá introducir

```
#? passwd
```

Se le pedirá entonces dos veces que introduzca la contraseña nueva.

Ahora puede crear los grupos que desee (vea sec:arch-grupo-nuevo). El único que a veces necesito y
no viene de serie es kodi; los demás, como power, wheel, etc., vienen de serie en Arch Linux.

También puede crear una cuenta de usuario ahora, si lo desea (vea subsec:arch-crear-usuario). Es
conveniente que el grupo principal al que pertenezca el usuario sea users y también hago que pertenezca a los
grupos wheel y power:

```
#? useradd -m -g users -G wheel,power,ftp,audio,video,storage,network \
> -k /etc/skel/ -s /bin/bash username
```

Ahora lo que suelo hacer es crear al usuario ctafur con permisos de administración que se los voy dando con
Polkit. Una vez que le he dado los permisos necesarios, lo usaré para configurar el sistema. El usuario alarm
viene de forma predeterminada. Este usuario será el que use para el uso normal del sistema. Eso sí, lo añadiré
también a algunos de los grupos, como power, ftp, audio, video y storage.

También deberá crear una contraseña para dicho usuario:

```
#? passwd username
```

Se le pedirá dos veces que introduzca la contraseña que desea.

Si desea poder emplear el comando #? para poder dar privilegios de administración a ciertos usuarios, tendrá
que instalar el paquete sudo:

```
#? pacman -S sudo
```

Una vez instalado sudo, deberá editar el fichero /etc/sudoers, pero éste es un fichero especial y debe editarlo
con el comando `visudo`, que, entre otras cosas, impide que varios usuarios puedan editar dicho fichero al mismo
tiempo; porque, recuerde, Linux es multiusuario en todas sus versiones; tampoco se le permitiría editar
/etc/sudoers desde root si no cambia los permisos de dicho fichero. Para ello, deberá usar el comando:

```
#? visudo
```

Una vez dentro, descomente (elimine el símbolo de almohadilla (`#`) de)

```bash
##
## User privilege specification
##
#%wheel ALL=(ALL) ALL
```

Si también lo desea, puede dar privilegios de forma individual a los usuarios añadiendo lo siguiente:

```bash
username ALL=(ALL) ALL
```

Pero yo prefiero dar privilegios añadiéndolos al grupo wheel; así es más rápido. Otra cosa que suelo hacer es
dar privilegios a los usuarios del grupo power para que puedan apagar, reiniciar y suspender el sistema sin que
se les pida que introduzcan su contraseña. Para ello, añada a la última línea lo siguiente:

```bash
%power hostname=(ALL) NOPASSWD: /usr/bin/systemctl poweroff,/usr/bin/systemctl halt,/usr/bin/systemctl reboot
```

Una vez habilitado `sudo`, es aconsejable configurar varias cosas en /etc/bash.bashrc. Una es cambiar el tipo de
terminal; la otra, la creación de algunos alias. Para ello, necesitará añadir lo suguiente al fichero
/etc/bash.bashrc:

```bash
set -o vi
TERM=xterm-256color
alias l2='ls -lah'
alias rboot2='sudo systemctl reboot'
alias pwoff2='sudo systemctl poweroff'
alias hlt2='sudo systemctl halt'
```

Tras reiniciar, podrá usar estos comandos y podrá también usar la funcionalidad tab completion con ellos.
Intenté introducir, también, `alias ls='ls --color=auto'` para que también se mostrara con colores los
resultados del comando `ls` para root, pero no funcionaba. Aun así, pensándolo bien, creo que es buena idea que
se diferencie visualmente lo que hago como root, así que lo dejaré así. Para comprobar que su terminal tiene
ahora 256 colores, puede introducir el comando:

```
$ tput colors
```

También podría crear configuraciones personalizadas para cada usuario, creando y/o modificando el
fichero /home/username/.bashrc.

Creo que es bueno que ahora reinicie el sistema. A partir de ahora, es preferible hacer las cosas desde el
usuario username en lugar de desde root, por seguridad. Aunque aquí vea el prompt `#`, lo único que quiere decir
es que el comando debe ejecutarse con privilegios de administración; podría ejecutarlos desde una sesión de
usuario precediéndolos de `sudo`. Así que lo mejor es reiniciar el sistema y entrar por sesión de SSH con el
usuario username.

También suelo instalar Vim, es decir, Vi Improved. Para ello, basta con introducir:

```
#? pacman -S vim vim-spell-en vim-spell-es vim-colorsamplerpack
```

Luego, modifique el fichero de configuración global de Vim para todo el sistema. Dicho fichero es /etc/vimrc. En
éste, añada lo siguiente:

```vim
syntax on
:set encoding=utf8
:set fileencoding=utf8
:set textwidth=112
:set tabstop=2
:set softtabstop=2
:set ai shiftwidth=2
:set t_Co=256
:set expandtab
colorscheme xoria256
```

Ésta es la configuración que suelo usar. Si quiere ver qué hace cada línea, puede buscar en internet.  También
podría crear un fichero de configuración distinto para cada usuario, al igual que se dijo antes para la
configuración de BASH. En tal caso, debería crear el fichero /home/username/.vimrc y añadir las configuraciones
que desee para el usuario username.

Otro paquete que puede venir bien instalar es base-devel, pero tampoco es tan necesario; depende de la función a
la que quiera asignar el sistema:

```
#? pacman -S base-devel
```

Una vez instalado, sería conveniente reiniciar el sistema. El reinicio, lógicamente, terminará la sesión de SSH
y tendrá que volver a entrar.
