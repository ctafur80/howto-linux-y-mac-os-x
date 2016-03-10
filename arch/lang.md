## Configuración de idiomas

Esta explicación está sacada principalmente de la entrada [Locale](https://wiki.archlinux.org/index.php/Locale)
del wiki de Arch Linux, aunque he terminado consultando muchas otras fuentes para hacer cosas que no vienen ahí.

Tengo un directorio que contiene un script y dos locales personalizados. Con esto, es muy sencillo el proceso de
configuración de mis sistemas a mi gusto. Lo primero que debe hacer es copiar a su RbPi, desde mi ordenador, el
directorio ~/Dropbox/Documentos/rasp/locales-total/ con los tres ficheros que alberga. Puede copiarlos en su
directorio home, por ejemplo; sería así:

```bash
#? scp -r ~/Dropbox/Documentos/rasp/{{nombreRpi}}/* root@nRpI:/home/{{nombreUsuario}}
```
La opción `-r`, además de copiar los directorios recursivamente, sigue los enlaces simbólicos, que es lo que
interesa aquí.

Ahora,

```bash
$ cd /home/uSernamE/locales-total/
$ sh locales-total
```

Cuando termine, reinicie su sistema y ya está. El script muestra al final la lista de los valores de
sus variables de entorno de locales. En el proceso, como podrá ver, he usado locales personalizados nuevos
(zfur_en_US.UTF8 y zfur_es_ES.UTF8), en lugar de modificar los que ya existen. Creo que es mejor,
puesto que los predefinidos a veces se actualizan al actualizar el sistema y echan a perder mis modificaciones.

No obstante, ahora explicaré todo el proceso, por si quiere hacer alguna variación. Lo primero que debe hacer es
ver qué locales tiene generados, que no es lo mismo que los que tiene habilitados. Para ello, deberá introducir:

```bash
$ localectl list-locales
```

Lo normal es que, tras una instalación limpia, no tenga ninguno. En realidad usa uno llamado `C`, que hará
referencia al idioma inglés, pues es el idioma que viene predefinido, aunque no aparece con este comando
(aparecería con `locales -a`). Ahora sólo una prueba: `locales -a`.

Para poder generar los locales creados por mí, es decir, zfur_en_US.UTF8 y zfur_es_ES.UTF8, deberá copiarlos
al directorio /usr/share/i18n/locales/:

```bash
#? cp /home/{{nombreUsuario}}/zfur_* /usr/share/i18n/locales/
```

Puede ver también la lista de todos los que se pueden generar y habilitar en su sistema editando
/etc/locale.gen. Las entradas que aparecen con el símbolo `#` a la izquierda (es decir, las comentadas) son las
que no se generan. Puede descomentar (es decir, quitar `#` de) las que desee generar. Yo, en mis RbPi, uso:

```
en_US.ISO-88591-1
en_US.UTF-8 UTF-8
es_ES.ISO-88591-1
es_ES.UTF-8 UTF-8
zfur_en_US.UTF-8 UTF-8
zfur_es_ES.UTF-8 UTF-8
```
Las entradas zfur_es_ES.UTF-8 UTF-8 y zfur_es_ES.UTF-8 UTF-8 no se han descomentado, sino que he tenido que
escribirlas, como es lógico. Ahora podrá generar los locales. Para ello, introduzca:

```bash
#? locale-gen
```
Ojo, debe usar siempre este comando para generar los locales nuevos; no vale con reiniciar.

Para mostrar ahora, para la cuenta de usuario desde la que está en el shell, el locale que tiene fijado
actualmente y los valores de sus variables de entorno de locales, introduzca:

```bash
$ localectl list-locales
```
Si ha configurado los mismos que yo, deberá aparecerle lo siguiente:

```bash
$ localectl list-locales
en_US
en_US.iso88591
en_US.utf8
es_ES
es_ES.iso88591
es_ES.utf8
spanish
zfur_en_US.utf8
zfur_es_ES.utf8
```
El locale que se usará, elegido entre los generados previamente, está establecido en los ficheros locale.conf,
cada uno de los cuales debe contener una lista de establecimiento de valores a variables de entorno (cada una,
en una línea). Por ejemplo,

```
LANG=en_AU.UTF8
LC_COLLATE=C
LC_TIME=en_DK.UTF8
```

El locale para todo el sistema se puede establecer creando o editando /etc/locale.conf, pero yo prefiero usar el
comando `localectl`. Por ejemplo, puede usar:

```bash
#? localectl set-locale LANG=en_US.utf8 LANGUAGE=en_US LC_NUMERIC=zfur_es_ES.utf8 \
> LC_TIME=es_ES.utf8 LC_MONETARY=zfur_es_ES.utf8 LC_PAPER=es_ES.utf8 LC_NAME=es_ES.utf8 \
> LC_ADDRESS=es_ES.utf8 LC_TELEPHONE=es_ES.utf8 LC_MEASUREMENT=es_ES.utf8 \
> LC_IDENTIFICATION=es_ES.utf8
#? localectl set-keymap es
#? localectl set-x11-keymap es
```

Aunque aquí se ha dividido para que quepa en la hoja, se debe poner todo en un solo comando. Luego, con:

```bash
$ localectl status
```

podrá ver ćomo ha quedado. Esto dependerá de sus gustos. Yo prefiero tener los mensajes en inglés. De entre
estas variables de entorno, una de las más importantes es `LANG`, pues es la que se usará para las variables que
comienzan por `LC_` y no tienen un valor establecido de forma explícita.

Para cada usuario se puede establecer sus locales, que prevalecerán sobre los locales de todo el sistema. Para
esto, deberá crear o editar ~/.config/locale.conf. Puede hacer que se actualicen los locales de su usuario sin
necesidad de salir y volver a entrar, pero el proceso es algo complejo: yo aconsejo que salga y vuelva a entrar
a la sesión de ese usuario.

Tal y como hice al crear los locales `zfur_es_ES.UTF8` y `zfur_en_US.UTF8` basándome en `es_ES.UTF8` y
`en_US.UTF8`, puede customizar si lo desea algún locale instalado en su sistema. Para ello, lo primero que
deberá hacer es crear el fichero de su nuevo locale en /usr/share/i18n/locales/. Yo suelo copiar uno sobre el
que me basaré y modificar el nombre. Por ejemplo, lo llamaré zfur_es_ES a uno basado en es_ES. Si desea que, por
ejemplo, en zfur_es_ES se use el punto como separador de la parte entera de la fraccionaria en los numerales y
la separacion de 3 en tres de cifras grandes se haga con un espacio estrecho de no separación, cambie los
siguientes valores en dicho fichero:

```
LC_MONETARY
.
.
.
mon_decimal_point    "<U002E>"
mon_thousands_sep    "<U202F>"
.
.
.

LC_NUMERIC
decimal_point        "<U002E>"
thousands_sep        "<U202F>"
.
.
.
```

Lo que ve a la derecha son los códigos en hexadecimal de los símbolos que desea usar en cada caso,
para la codificación UTF-8 (de Unicode). También debe añadir la entrada

```
zfur_es_ES.UTF8 UTF-8
```

al fichero /etc/locale.gen. Ahora deberá regenerar los locales (recuerde, comando `locale-gen`) para que los
cambios tengan efecto tras el reinicio posterior.

Ahora debe reiniciar. No es necesario que sea root; puede hacerlo desde cualquier cuenta de usuario siempre y
cuando preceda de `sudo` al comando. El comando tendrá efecto en todo el sistema. Es decir, cuando reinicie, si
introduce el comando `localectl list-locales`, verá que ha cambiado la configuración de todos los usuarios.
Puede comprobar que efectivamente ha cambiado a español la variable `LC_TIME` introduciendo el comando:

```bash
$ date
```

Si la fecha aparece ahora en español es que lo ha hecho correctamente. También puede poner ahora como nombre
pretty de host un nombre con acentos gráficos; por ejemplo:

```bash
#? hostnamectl --pretty set-hostnme "Raspberry Pi 2 Para Descargas y Reproducción de Vídeos"
```

Otra cosa que suelo hacer es usar un mapeado del teclado modificado por mí. El único cambio que hago con
respecto al teclado normal y corriente en español es que intercambio las funciones de las teclas `bloq mayús` y
`esc`. Lo hago porque uso mucho Vim y me resulta más cómodo. Para hacer esto, debe primero copiar el contenido
del fichero /usr/share/kbd/keymaps/i386/qwerty/es.map.gz a un fichero, llamado, por ejemplo, zfur_es.map.gz, en
el mismo directorio. Luego edite este último y deje así los siguientes códigos de teclas:

```
.
.
.
keycode   1 = Caps_Lock
.
.
.
keycode   58 = Escape
  alt     keycode   58 = Meta_Escape
.
.
.
```

Luego deberá establecer los locales, pero no como lo hizo antes, ahora deberá seleccionar el mapeado de teclas
zfur_es, en lugar de es, tanto en el mapeado de teclas de la consola virtual (`VC Keymap`), como en el de la
disposición en X11 (`X11 Layout`):

```bash
#? localectl set-locale LANG=en_US.utf8 LANGUAGE=en_US LC_ALL=en_US.UTF-8
#? localectl set-keymap zfur_es
#? localectl set-x11-keymap zfur_es
```

Deberá comprobar que ha realizado correctamente los cambios con el siguiente comando:

```bash
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

En Mac OS X lo mejor es que añada a su fichero de configuración de su shell (si es Bash, suele ser ~/.bashrc) lo
siguiente:

```bash
#? export LC_ALL=en_US.UTF-8
#? export LANG=en_US.UTF-8
```

Recuerde que, para entrar con Mosh a una sesión remota de otro sistema, deberán ser compatibles los locales de
ambos sistemas. Si no desea configurarlos, puede hacer entonces lo siguiente:

```bash
$ mosh root@server4 --server="LANG=$LANG mosh-server"
```
