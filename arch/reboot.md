## Reiniciar el sistema

Para reiniciar su sistema Arch Linux, deberá introducir el comando

```
#? systemctl reboot
```

El comando `systemctl` es del nuevo sistema init Systemd, y es mejor usar este comando que

```
#? reboot
```

pues Systemd finaliza del modo correcto los servicios; cosa que no hacían los comandos antiguos. En cuanto a los
permisos, como ve, el prompt que muestro aquí para estos comandos es `#?`, lo cual indica que un usuario normal
no tiene permisos para ejecutar estos comandos a menos que un administrador se los dé. Lo normal es incluirlos
en el grupo power y, mediante Polkit, hacer que los miembros de dicho grupo puedan usar estos comandos.
