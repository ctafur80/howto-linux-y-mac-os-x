## Habilitar autocompletado de comandos

Para habilitar el autocompletado de comandos en su shell, es decir, que, teniendo a medio escribir un comando en
su shell, pulse la tecla *tab* y se termine de completar el comando, o en caso de que existan varias
posibilidades, se complete hasta el punto en que coincidan dichas posibilidades y se muestren debajo dichas
posibilidades, en principio no debe hacer nada en su sistema Arch Linux, pues ya viene de serie. El problema es
que no viene completo. Por ejemplo, si introduce `sudo` o `man`, lo siguiente ya no tendrá autocompletado. Para
solucionar esto, basta con que instale en su sistema el paquete `bash-completion`.

Antes, solía modificar mi fichero .bashrc con lo siguiente:

```
complete -cf sudo
complete -cd man
```

Pero es mejor instalar el paquete y olvidarse, además de que no tengo que ir modificándolo "a mano" por cada
software que instalo.
