## Instalar el cliente de BitTorrent Deluge

La documentación para la instalación del cliente de BitTorrent Deluge la he sacado principalmente del sitio
[How-To Geek](www.howtogeek.com/142044/how-to-turn-a-raspberry-pi-into-an-always-on-bittorrent-box/) y del
[wiki oficial de Arch Linux](https://wiki.archlinux.org/index.php/Deluge).

Lo primero será instalar Deluge tal y como se instala el resto de software desde los repositorios:

```
#? pacman -S deluge python2-service-identity
```

Un paquete que no me instaló como dependencia, pero que si no está instalado sale una advertencia sobre la
conveniencia de instalarlo, cuando se ejecuta el cliente de consola de Deluge, es `python2-service-identity`.
Para iniciar el demonio deluged, introduzca:

```
#? systemctl start deluged.service
```

Este comando hará que se inicie el demonio deluged como usuario deluged. Puede introducir

```
$ systemctl status deluged.service
```

para ver que el demonio deluged se ha cargado correctamente.

Si desea no tener que iniciar una sesión con el usuario deluged (que ni si quiera lo habrá creado ni desee
crearlo, seguramente) para poder usar Deluge,

```
#? cp /usr/lib/systemd/system/deluged.service /etc/systemd/system/
```

Ahora, en /etc/systemd/system/deluged.service, dé a User el valor que desee:

```
User=nOmdelugE
```

Puede habilitar el demonio `deluged` (que también se conoce como `deluged.service`) para que se inicie al
iniciar el sistema. No obstante, al iniciar dicho demonio, comenzarán las descargas y/o subidas por BitTorrent,
y eso consume casi todos los recursos de la pobre RbPi. En caso de que quiera iniciarlo al arrancar el sistema
para todo el sistema, tendrá que introducir el comando:

```
#? sudo systemctl enable deluged.service
```

Yo soy partidario de habilitarlo para que se arranque al iniciar el sistema, pues los dos únicos propósitos que
tiene para mí esta RbPi es descargar ficheros por BitTorrent y reproducir videos. Aún así, aunque esté
descargando ficheros, se pueden reproducir películas, aunque se puede quedar colgado el sistema si se pasa muy
hacia delante rápidamente. Si quiere asegurarse de que Deluge no está funcionando mientras visualiza algún
video, puede detener momentáneamente el demonio deluged con:

```
#? systemctl stop deluged.service
```

Otra forma de detener el demonio es con la orden `halt` dentro de deluge-console, pero me parece más cómoda la
anterior. Para que vuelva a ejecutarse el demonio, basta con poner:

```
#? systemctl start deluged.service
```

Ahora reinicie el demonio deluged, logueese como `nOmdelugE` e inicie el cliente de consola de Deluge:

```
$ deluge-console
```

Verá que suele tardar un rato en arrancar. En este cliente debe aparecer un mensaje que diga que está conectado
al puerto 58846 de su interfaz loopback. Ahora, desde esta consola, introduzca:

```
>>> config -s allow_remote True
Setting allow_remote to True..
Configuration value successfully updated.
* ConfigValueChanged: allow_remote: True
```

si desea poder gestionarlo con el cliente de escritorio desde otro ordenador. Tras introducir el comando,
debería aparecer `True`. Si puso `False`, es que algo falló. Ahora salga del cliente de consola:

```
>>> exit
```

Puede comprobar también, si le apetece, que se ha creado el directorio ~/.config/deluge/ con varios ficheros de
configuración de la aplicación.

Ahora,

```
#? systemctl restart deluge
```

Ahora puede instalar el cliente GUI de Deluge en su ordenador e introducir los datos de su cuenta de usuario
para que pueda empezar a indicarle a su Raspberry Pi 2 qué ficheros desea que descargue por BitTorrent, o, si lo
prefiere, puede añadirlos por una sesión SSH mediante el comando deluge-console; lo único es que tendrá que
copiar al RbPi los ficheros .torrent mediante algún comando como `scp`, por ejemplo.

Desde el propio cliente de consola puede cambiar la configuración de Deluge. Por ejemplo, si desea que los
ficheros se descarguen a /home/nOmdelugE/downloads en lugar de a /home/nOmdelugE/Downloads, deberá introducir,
desde la interfaz de deluge-console:

```
>>> config -s torrentfiles_location /home/nOmdelugE/downloads
>>> config -s move_completed_path /home/nOmdelugE/downloads
>>> config -s download_location /home/nOmdelugE/downloads
>>> config -s autoadd_location /home/nOmdelugE/downloads
```

También puede configurar el número máximo de descargas, etc. Algo que he visto al seguir este punto es que se
crea el fichero temporal /home/nOmdelugE/.config/deluge/core.conf~. Se puede eliminar, deteniendo antes el
demonio deluged, y no pasa nada.

Ahora, si lo desea, puede instalar el reproductor OmxPlayer para poder empezar a ver películas si conecta a un
televisor o monitor su RbPi:

```
#? pacman -S omxplayer
```

Antes de proceder a la instalación, se nos preguntará por los drivers gráficos que queremos instalar y las
librerías del códec h.264. Yo lo dejo con las opciones predeterminadas. Luego se ve que también instala un
montón de paquetes más que imagino que son de las X. Para que en OmxPlayer se vean los subtitulos, hay que
instalar el paquete `ttf-freefont`, pues éstas son las fuentes que usa dicho programa. Ahora puede actualizar la
caché de fuentes de su sistema con el comando:

```
#? fc-cache -fv
```

y comprobar reproduciendo un vídeo con subtítulos que las fuentes TTF Freefont se han instalado correctamente.

Tenía un pequeño problema con OmxPlayer que consistía en que con algunos videos se seguía viendo en la franja
negra de arriba como fondo la consola del sistema. Con la opción `-b` se soluciona. Es decir, ejecutando el
comando:

```
$ omxplayer -b nomVideo
```

Para que se vean subtítulos, le añado la opción `--subtitle`:

```
$ omxplayer -b --subtitle nomSubs nomVideo
```

Puede pausar con la barra espaciadora, pasar hacia adelante con la flecha hacia la derecha y atrás con la de la
izquierda, salir con q, etc. Puede ver el man de omxplayer para aprender a usarlo. Si no se ven bien los
subtítulos, pruebe a ver qué formato tienen con el comando:

```
$ file -i subs
```

donde subs es el fichero de subtítulos. Si desea pasarlos a UTF-8, puede usar el comando `iconv`.

Un buen sitio para descargar torrents era [éste](www.baygle.org). Parece ser que ya no funciona.

Otra de las cosas que es conveniente hacer, si se va a usar, además, como servidor de ficheros y/o de impresión
con el protocolo SMB/CIFS (cuya implementación en Linux tiene el pintoresco nombre de Samba), es reducirle a 16
MiB la memoria de vídeo. Para esto, deberá editar el fichero /boot/config.txt y poner

```
gpu_mem=16
```

Si está comentada dicha línea, descomentela y asígnele ese valor (16); creo que el valor predeterminado es 64.
Si quisiese usar su RbPi como media center, debería darle un valor superior, como, por ejemplo, 320, pero lo que
tengo claro tras bastantes pruebas es que el RbPi no tiene la suficiente potencia de procesamiento como para
trabajar como cliente de descargas de BitTorrent y media center Kodi a la vez (lo que sí se puede es reproducir
vídeos mediante Omxplayer, por ejemplo, pero es más engorroso que usar Kodi, que es una maravilla).

En ese mismo fichero (/boot/config.txt) puede configurar su sistema para overclockearlo (vea TKTKTK).
