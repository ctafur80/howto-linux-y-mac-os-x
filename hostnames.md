## Cambiar los nombres de host

Para cambiar los nombres de host de cualquier sistema que use Systemd, lo mejor es usar los siguientes comandos:

```
#? hostnamectl --static --transient set-hostname hOstnamE
#? hostnamectl --pretty set-hostname "dEscripcióN"
```

Estos comandos son de Systemd y cambian los tres nombres de host. Con

```
$ hostnamectl --static
$ hostnamectl --transient
$ hostnamectl --pretty
```
podrá comprobar que ha establecido los nombres que pretendía. Si aún no ha configurado los locales en su sistema
a su gusto (vea [este tutorial](./lang.md)), puede que el pretty hostname no aparezca como deseaba. Una vez
cambiados estos nombres, reinicie el sistema y comprobará que el prompt del shell mostrará el nuevo static
hostname.
