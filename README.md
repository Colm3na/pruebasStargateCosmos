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

### Compilando:

> La última versión es [stargate-3](https://github.com/cosmos/gaia/releases/tag/stargate-3) pero comprobad siempre en el [repositorio oficial](https://github.com/cosmosdevs/stargate#testnet) por si este repositorio se quedara atrasado.

> Necesitamos `Go` instalado, podéis encontrar más información sobre la instalación [aquí](https://github.com/Colm3na/MeetupCosmos), o en su [web oficial](https://golang.org/doc/install).
```sh
#clonamos el repositorio
git clone https://github.com/cosmos/gaia && cd gaia/

#nos situamos en la rama correcta
git checkout stargate-3

#compilamos
make build
```

### Creando los primeros archivos de configuración e iniciando el nodo:

> Cambiad `DelegaStargate` por el nombre que le querais poner a vuestro nodo.
```sh
cd gaia/build

./gaiad init DelegaStargate
```

- Eliminamos el génesis creado y nos descargamos el correcto:
```sh
cd .gaiad/config

rm genesis.json

wget https://raw.githubusercontent.com/cosmosdevs/stargate/master/genesis.json
```

> El génesis podemos encontrarlo [aquí](https://github.com/cosmosdevs/stargate/blob/master/genesis.json) y el `sha256sum` _(muy importante comprobarlo)_ es:
```sh
sha256sum genesis.json

346dac9029a686845ee7ffd711a0c2479a6422c10f92f31ded6da8c6f4efbbc5  genesis.json
```

- Añadimos `peers` para conecectarnos _(el archivo está situado en `.gaiad/config`, más info [aquí](https://github.com/cosmosdevs/stargate#testnet))_:

```sh
00d8e9c0df367296436854b580d9b069d3f1a5fd@34.123.30.100:26656
```

- Resetamos el nodo:
```sh
./gaia/build/gaiad unsafe-reset-all
```

- Iniciando el nodo:
```sh
./gaia/build/gaiad start
```

- Para más comodidad y asegurarnos que el nodo siempre estará funcionando podemos crear un servicio de  sistema:
```sh
cd /etc/systemd/system
```

- Creamos el archivo con `sudo vim gaiad.service` y copiamos dentro _(como siempre cambiad USER por vuestro usuario)_:
```sh
[Unit]
Description=Cosmos Gaia Node
After=network.target

[Service]
Type=simple
User=USER
WorkingDirectory=/home/USER
ExecStart=/home/USER/gaia/build/gaiad start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
```

- Iniciamos el servicio de sistema:
```sh
sudo systemctl start gaiad.service
```

- Activamos el servicio del sistema desde el arranque:
```sh
sudo systemctl enable gaiad.service
```

> Podemos ver los logs del nodo con:
```sh
sudo journalctl -f -u gaiad.service
```

### Iniciando nuestro validador:
> En progreso =) ...