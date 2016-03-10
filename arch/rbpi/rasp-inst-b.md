## Instalación básica de Arch Linux en un RbPi desde un sistema Linux

La instalación básica desde Linux está basada en la explicación de la propia página oficial de [Arch
Linux](http://archlinuxarm.org/platforms/armv7/broadcom/raspberry-pi-2).

La instalación inicial se hace directamente sobre la tarjeta microSD, pero antes de nada, deberá tener
habilitada en su sistema Linux la cuenta de root, cosa que no es cierta siempre; por ejemplo, Ubuntu la trae
deshabilitada (vea Habilitar la cuenta de root). Si desea, tras la instalación básica de Arch Linux en su RbPi,
puede deshabilitar la cuenta de root en su sistema Linux (vea Deshabilitar cuenta root). No vale con simular que
tiene una cuenta root (vea Simular cuenta root).


  * Introduzca en un lector de tarjetas microSD de su ordenador la tarjeta microSD que desea emplear para
    instalar Arch Linux en su RbPi. Si no tiene instalado el software para poder operar con medios con sistema
    de ficheros exFAT en su ordenador y esta tarjeta tiene particiones en dicho formato, aparecerá un mensaje de
    error al insertarla si su sistema intenta montarla automáticamente. No se preocupe; puede ignorar dicho
    mensaje y seguir con los pasos siguientes.

  * Antes de nada, debe averiguar los nombres descriptores del kernel (*kernel name descriptors*, que son los
    ficheros que están en /dev/) de las particiones de su tarjeta microSD. Para esto, puede usar el comando

    ```
    $ lsblk
    ```

    También verá si alguna(s) de esta(s) partición/particiones está(n) montada(s) en su sistema. Si no está
    seguro de cuál es la ruta de  dispositivo de la tarjeta microSD, no siga, pues puede que por equivocación
    termine eliminando particiones importantes de su sistema.

  * En esta explicación se supondrá que dicha ruta es /dev/sd{{X}}; ese {{X}} es un carácter genérico que puede
    tener como valor *a*, *b*, etc. Una vez que sepa a ciencia cierta cuál es la ruta de su fichero de
    dispositivo de su tarjeta microSD, si según el comando `lsblk`  había montada alguna de las particiones de
    dicha tarjeta microSD, desmóntela mediante el shell con el comando `umount`:

    ```
    #? umount /dev/sd{{X}}[1-{{n}}]
    ```

    donde `{{n}}` es el número mayor de su partición del dispositivo /dev/sd{{X}}; si la numeración de dichas
    particiones según sus nombres descriptores del kernel no es consecutiva, desmóntelas una a una. Si trata de
    desmontar estas particiones desde la interfaz gráfica, puede que lo desmonte completamente y no aparezcan
    los ficheros de dispositivo para dicha tarjeta cuando los muestra con el comando `lsblk`, con lo cual, a
    efectos prácticos, es como si hubiera extraído la tarjeta microSD de su ordenador. Si su sistema no intentó
    montar nada, mejor; no tendrá que hacer lo dicho.

  * Ahora, ejecute el comando `fdisk` (como root o con `sudo`) de /dev/sd{{X}}:

    ```
    #? fdisk /dev/sd{{X}}
    ```

  * Una vez en el entorno del comando `fdisk`, aparecerá `Command (m for help):`.  Entonces deberá borrar las
    particiones existentes y crear una nueva. Para hacer esto, deberá seguir los siguientes pasos (advierta que
    los cambios no tendrán efecto hasta que no introduzca la opción de la letra `w`):


    * Escriba `o` y pulse intro. Esto borrará las particiones existentes en esta unidad (la del dispositivo
      /dev/sd{{X}}).

    * Escriba `p` y pulse intro. Esto muestra cuáles serían las particiones según los cambios que ha hecho con
      `fdisk` hasta ahora (recuerde que los cambios no tendrán efecto mientras no use la opción `w`); ahora
      mismo, no debería haber particiones, pues el comando `o` se supone que las ha "borrado".

    * Escriba `n` y pulse intro; luego, para el tipo de partición, escriba `p` (de *primary*) y pulse intro, y,
      para el número de partición, escriba `1` y pulse intro, para que esta partición que está creando sea la 1
      (dev/sd{{X}}1). En `first sector`, acepte el valor predeterminado (`default`), por tanto, sólo tendrá que
      pulsar intro (el valor del sector es `2048`) y, para el último sector, introduzca `+100M` y pulse intro,
      con lo que tendrá un tamaño de 100 MiB en la partición.

    * Ahora escriba `t` y pulse intro; esto cambia el system id de la partición. Luego, escriba `c` y pulse
      intro, para que establezca el tipo de partición como `W95 FAT32 (LBA)`; es decir, una partición del tipo
      FAT.

    * Ahora debe crear otra partición, para lo que deberá pulsar `n` y luego intro. Luego, `p` y después intro,
      para que la partición sea primaria como la anterior, y luego `2` y después intro, para que la partición
      sea la segunda (/dev/sd{{X}}2). Ahora, para seleccionar el primer sector, deberá pulsar intro para que se
      acepte el valor predeterminado, que debe ser de `206848` (número de sector), es decir, el primer sector
      justo a continuación de la partición anterior, y, para el último sector, asigne el tamaño que desee. Si
      quiere crear otra partición para algún uso particular, no podrá usar todo el tamaño restante para ésta.
      Creo que asignando a esta 8 GiB (`+8G`) se tiene de sobra para instalar todo el software que vaya a
      necesitar. No obstante, si lo prefiere, puede asignar a esta partición todo el tamaño restante y luego
      reparticionarla mediante el uso de software de reparticionado, como, por ejemplo, Parted o gParted.

    * Ahora, si no ha usado todo el tamaño restante para la partición anterior, cree otra partición de Linux
      como la anterior sólo que esta vez en el último paso sí deberá seleccionar el valor predeterminado. Esta
      partición será la /dev/sd{{X}}3.

    * Ahora introduzca `w` y luego pulse intro para que se escriba la tabla de particiones en la tarjeta
      microSD y se cierre el programa Fdisk.

  * Ahora debe crear y montar el sistema de ficheros FAT16. Antes de hacerlo, si tiene montada alguna de las
    particiones de /dev/sd{{X}}, debe desmontarlas con el comando `umount` desde el shell, aunque se le dijo
    antes que las desmontara:

    ```
    #? mkfs.vfat /dev/sd{{X}}1
    #? mkdir boot/
    #? mount /dev/sd{{X}}1 boot/
    ```

    Con esto ha creado el sistema de ficheros FAT16 en la partición /dev/sd{{X}}1, luego, el directorio boot/
    (en la ruta desde la que haya introducido el comando) y ha montado dicho sistema de ficheros en el
    directorio que acaba de crear.

  * Ahora haga lo mismo con el sistema de ficheros EXT4 en la otra partición y con el directorio root/:

    ```
    #? mkfs.ext4 /dev/sd{{X}}2
    #? mkdir root/
    #? mount /dev/sd{{X}}2 root/
    ```

    También puede dar formato ahora a la tercera partición si la creó:

    ```
    #? mkfs.ext4 /dev/sd{{X}}3
    ```

    Podría haber usado otro sistema de ficheros que sirva para Linux. No tiene que montar la partición. Si lo
    prefiere no le dé el formato ahora a la última partición y déselo desde el propio RbPi cuando tenga el
    sistema básico instalado. A mí me parece más cómodo hacerlo ahora.

  * Ahora elimine la imagen que tenga y descargue la última que exista, que deberá luego copiar a la tarjeta
    microSD:

    ```
    $ rm ArchLinuxARM-rpi-2-latest.tar.gz
    $ wget http://archlinuxarm.org/os/ArchLinuxARM-rpi-2-latest.tar.gz
    ```

    Lo nomrmal es que lo haga desde su directorio home, aunque podría hacerlo desde cualquier otro sobre el que
    tenga los privilegios pertinentes. Ahora toca descomprimir y desarchivar la imagen al directorio
    root/. Ésta es la parte que debe hacer necesariamente como root y no vale hacerlo con `sudo`:

    ```
    # bsdtar -xpf ArchLinuxARM-rpi-2-latest.tar.gz -C root/
    # sync
    ```

  * Mueva ahora los ficheros de arranque a la primera partición (es decir, a /dev/sd{{X}}1):

    ```
    $ mv root/boot/* boot/
    ```

  * Desmonte ahora las dos particiones:

    ```
    #? umount boot/ root/
    ```

  * Extraiga la tarjeta microSD, introdúzcala en su RbPi, conecte el cable de red Ethernet a su RbPi y luego
    conéctele el cable microUSB de alimentación. Antes, es aconsejable asegurarse de que no tiene conectado nada
    a los puertos de la RbPi; lo que haya que instalar, como, por ejemplo, las tarjetas wifi, lo instalará
    luego.

  * Ahora, si conoce la dirección IP que le ha asignado su router a la interfaz del puerto Ethernet de su RbPi
    (lo normal es que se llame eth0), puede conectarse al mismo, desde su ordenador, mediante una sesión SSH
    (secure shell). Es aconsejable, no obstante, que averigue las direcciones MAC de la(s) interfaz/interfaces
    de red de su RbPi y en su router les asocie una dirección IP estática, para poder entrar fácilmente mediante
    SSH y para casi cualquier propósito. Además, si lo desea, puede modificar su fichero /etc/hosts para poder
    referirse a la(s) dirección/direcciones IP de su RbPi mediante un nombre. Desde Mac OS, deberá configurar el
    fichero /private/etc/hosts.

  * Si lo desea, para mantener limpio su ordenador, puede ahora eliminar los directorios boot/ y root/ (vea
    eliminar un directorio), deshabilitar la cuenta de root (vea deshabilitar la cuenta de root) y eliminar el
    fichero que descargó para la instalación (ArchLinuxARM-rpi-2-latest.tar.gz).
