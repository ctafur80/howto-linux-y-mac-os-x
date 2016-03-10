## Cambiar los nombres de host

Para cambiar los nombres de host de cualquier sistema que use el init Systemd, lo mejor es usar los siguientes
comandos:

```bash
#? hostnamectl --static --transient set-hostname {{hostName}}
#? hostnamectl --pretty set-hostname "{{descripción}}"
```

Estos comandos son de Systemd y cambian los tres nombres de host. Con

```bash
$ hostnamectl --static
$ hostnamectl --transient
$ hostnamectl --pretty
```
podrá comprobar que ha establecido los nombres que pretendía. Si aún no ha configurado los [locales](lang.md) en
su sistema a su gusto, puede que el pretty hostname no aparezca como deseaba. Una vez cambiados estos nombres,
reinicie el sistema y comprobará que el prompt del shell mostrará el nuevo static hostname.
