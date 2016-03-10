## Configurar la fecha y la hora

Si su sistema usa como init Systemd, la fecha y la hora se adquieren por NTP (*network time protocol*). El
demonio que ejecuta dicho protocolo es `systemd-timesyncd`. Para Arch Linux no es necesario que cambie nada de
la configuración de los servidores de NTP, pues funciona perfectamente con la que trae. Sin embargo, si desea
configurar su sistema para que emplee el huso horario de la España peninsular, deberá introducir

```bash
#? timedatectl set-timezone Europe/Madrid
```

Una vez hecho esto, puede comprobar si su sistema tiene la hora que desea, introduciendo

```bash
$ date
```
