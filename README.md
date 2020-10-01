# Repositorio para las pruebas de la testnet Stargate:

> [Este](https://github.com/cosmosdevs/stargate#testnet) es el repositorio oficial de Cosmos con más información.

> Es importante entender que esta versión tiene un solo binario en vez de `gaiacli / gaiad` como en versiones anteriores, el cual está situado en `/gaiad/build` bajo el nombre de `gaiad` para ejecutarlo nos situamos en esa carpeta y escribimos `./gaiad` o desde el `home` sería `./gaia/build/gaiad`. También encontramos un archivo `app.toml` mucho mas configurable.

> Para facilitarme el uso de los comandos en mi caso he añadido un [alias](https://es.wikipedia.org/wiki/Alias_(Unix)) en el `.profile`. Lo he hecho abriendo el archivo de configuración _(uso `vim` pero podeis usar `nano` o el que más os guste)_ `vim .profile` y añadiendo al final del mismo lo siguiente _(cambiad USER por vuestro usuario)_:
```sh
alias gaia="/home/USER/gaia/build/gaiad"
```

> Recargamos la terminal con `source .profile` o salimos y volvemos a entrar en la terminal.

> Más información sobre las opciones de configuración de `statesync` [aquí](https://docs.tendermint.com/master/tendermint-core/state-sync.html).

> Si obtenemos un error parecido a [este](https://github.com/Kava-Labs/kava/issues/656) necesitamos instalar `build-essential` con `sudo apt install -y build-essential`.

### [Grupo](https://t.me/Cosmos_Stargate) de Telegram de anuncios de Stargate.


### Dependencias:

Necesitamos `Go` instalado, podéis encontrar más información sobre la instalación [aquí](https://github.com/Colm3na/MeetupCosmos), o en su [web oficial](https://golang.org/doc/install).

Para algunas comprobaciones y consultas se usa `jq` _(no es necesario)_:
```sh
sudo apt install -y jq 
```

### Compilando:

> La última versión es [stargate-3](https://github.com/cosmos/gaia/releases/tag/stargate-3) pero comprobad siempre en el [repositorio oficial](https://github.com/cosmosdevs/stargate#testnet) por si este repositorio se quedara atrasado.

```sh
#clonamos el repositorio
git clone https://github.com/cosmos/gaia && cd gaia/

#nos situamos en la rama correcta
git checkout stargate-3

#compilamos
make build
```

> Si obtenemos este error _(más info [aquí](https://github.com/Kava-Labs/kava/issues/656))_:
> 
> ```sh
> go: downloading github.com/zondax/hid v0.9.0
> go: downloading github.com/mimoo/StrobeGo v0.0.0-20181016162300-f8f6d4d2b643
> go: downloading gopkg.in/ini.v1 v1.51.0
> # github.com/zondax/hid
> ../go/pkg/mod/github.com/zondax/hid@v0.9.0/hid_enabled.go:22:11: fatal error: sys/poll.h: No such file or directory
>  #include <sys/poll.h>
>           ^~~~~~~~~~~~
> compilation terminated.
> Makefile:79: recipe for target 'install' failed
> make: *** [install] Error 2
> ```
>
> Podemos solucionarlo instalando `build-essential` y compilando de nuevo:
> ```sh
> sudo apt install build-essential -y
>
> make clean
>
> make install
> ```

#### Validadores:
- La información relacionada con los validadores se encuentra en el documento [validadores.md](/validadores/validadores.md).

#### Delegadores:
- La información relacionada con los delegadores se encuentra en el documento [delegadores.md](/delegadores/delegadores.md).
