# Información relacionada con los validadores.

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