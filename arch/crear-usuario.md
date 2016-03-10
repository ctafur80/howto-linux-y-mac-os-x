## Crear una cuenta de usuario

Para crear una cuenta nueva de usuario en su sistema Arch Linux, puede introducir lo siguiente:

```
#? useradd -m -g grupo-p [-G grupo1[,... [,grupoN]]] -k /etc/skel -s shell-path username
```

donde `username` es el nombre del usuario que desea crear. Entre las opciones que se han dado en este ejemplo
están que se cree el directorio home del usuario con los mismos contenidos que hay en etc/skel/, que el grupo
principal de dicho usuario sea `grupo-p`, que el shell sea el que tiene su ejecutable en la ruta absoluta
`shell-path` (por ejemplo, para Bash sería /bin/bash). También se especifica que el usuario pertenezca a otros
grupos, además de su grupo principal. Si no estoy equivocado, `username` no debe contener mayúsculas ni símbolos
de puntuación ni acentos gráficos, pero sí está permitido que tenga números siempre y cuando no lo sea el primer
carácter.
