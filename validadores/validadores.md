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

- Reseteamos el nodo:
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

- Creamos el archivo con `sudo vim gaiad.service` y copiamos dentro _(como siempre cambiad `USER` por vuestro usuario)_:
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

> Podemos ver los logs con:
```sh
sudo journalctl -f -u gaiad.service
```

### Iniciando el validador:

> Para crear el validador hacen falta algunos `umuon`, creamos la wallet con el comando _(`d1` es el nombre de la wallet)_: 
>
> `./gaia/build/gaiad keys add d1`. 
>
> Si necesitamos eliminar una wallet podemos introducir:
>
> `./gaia/build/gaiad keys delete d1`
>
> Para comprobar las wallets que tenemos en el nodo:
>
>`./gaia/build/gaiad keys list`
>
> Si necesitamos pedir algunos `muon` podemos hacerlo en el [grupo de Telegram](https://t.me/joinchat/IYdbxRRFYIkj9FR99X3-BA).

> Info para los validadores en la [documentación oficial](https://hub.cosmos.network/master/validators/overview.html#).

> Antes de crear el validador nuestro nodo debe estar sincronizado, podemos comprobarlo con el explorador de [Big-dipper](https://stargate.bigdipper.live/) para Stargate.

- Creando el validador:
```sh
./gaia/build/gaiad tx staking create-validator \ 
    --amount=1000000umuon \ 
    --pubkey=$(gaia/build/gaiad tendermint show-validator) \ 
    --moniker=DelegaNetworks \ 
    --chain-id=stargate-3a \ 
    --commission-rate="0.20" \ 
    --commission-max-rate="0.9" \ 
    --commission-max-change-rate="0.9" \  
    --min-self-delegation="1" \ 
    --from=d1 \ 
    --website=https://delega.io \  
    --identity=1BED7C08416A619F \ 
```

`--amount=1000000umuon` => Cantidad mínima para crear el validador, en testnet `1muon` = `1000000umuon` _(en mainnet `1atom` = `1000000uatom`)_

`--pubkey=$(gaiad tendermint show-validator)` => Muestra la información del validador de Tendermint de este nodo.

`--moniker=DelegaNetworks` => Nombre de nuestro nodo, se puede modificar después en el archivo situado en `.gaiad/config` con el nombre `config.toml`

`--chain-id=stargate-3a` => Es un identificador único para la cadena. Ayuda a diferenciar entre diferentes cadenas usando la misma versión del software. Podemos comprobarlo con el comando `./gaia/build/gaiad status`

`--commission-rate="0.20"` => Comisión del validador _(`0.2` = `2%`)_.

`--commission-max-rate="0.9"` => Comisión máxima del validador.

`--commission-max-change-rate="0.9"` => Cambio máximo de la comisión del validador _(cada 24h)_.

`--min-self-delegation="1"` => Delegación mínima.

`--from=d1` => Desde la wallet que se tienen los fondos.

`--website=https://delega.io` => Sitio web del validador.

`--identity=1BED7C08416A619F` => Puede ser usada como para verificar la identidad con sistemas como Keybase o UPort. Cuando se usa con Keybase `--identity` debe ser una cadena de 16 dígitos que se genera con una cuenta keybase.io. Es un método criptográficamente seguro para verificar su identidad a través de múltiples redes en línea. La API de Keybase permite recuperar su avatar de Keybase. Así es como se puede añadir un logo a tu perfil de validador.

> Cuando se especifican los parámetros de la comisión, la tasa de cambio máximo de comisión se utiliza para medir el cambio de puntos porcentuales sobre la tasa de comisión. Por ejemplo, del 1% al 2% es un aumento de la tasa del 100%, pero sólo un punto porcentual _(más info en la [documentación oficial](https://hub.cosmos.network/master/validators/validator-setup.html))_. Debe estar entre 0 y la tasa máxima de comisión del validador. No debe exceder la tasa de cambio máxima de comisión del validador, que es el máximo porcentaje de cambio de puntos por día. En otras palabras, un validador sólo puede cambiar su comisión una vez al día y dentro de los límites de la tasa de cambio máxima de comisión.

- Comprobamos que nuestro validador está funcionando:
```sh
./gaia/build/gaiad query tendermint-validator-set | grep "$(gaia/build/gaiad tendermint show-validator)"
```

- Editando o añadiendo información de nuestro validador:
```sh
./gaia/build/gaiad tx staking edit-validator \
    --moniker="nombre de tu nodo" \
    --website="Sitio web del validador" \
    --identity="Verificación de la identidad y logo con Keybase" \
    --details="Descripción de tu validador aquí" \
    --chain-id="Identificador de la cadena" \
    --from="tu wallet" \
```

> Puedes editar la descripción pública de tu validador. Esta información es para identificar a tu validador, y será utilizada por los delegadores para decidir a qué validadores apoyar. Asegúrate de proporcionar información para cada valor. Si no se incluye una valor en el comando, quedará en blanco por defecto _(`--moniker` se convierte en el nombre de la máquina)_ si nunca se ha configurado o permanecerá igual si se ha configurado en el pasado. `<key_name>` especifica qué validador está editando. Si eliges no incluir ciertos valores, recuerda que el valor `--from` debe ser incluido para identificar el validador a actualizar.

- Ver la descripción del validador:
```sh
./gaia/build/gaiad query staking validator cosmosvaloper15vyuq3ctwfks0wpg2gud4quq6xst8hcgf04t6y
```

> `cosmosvaloper...` es un valor que podemos comprobar en un explorador de bloques, por ejemplo [Big-dipper](https://stargate.bigdipper.live/).


- Si nuestro validador entra en `jail`:
```sh
./gaia/build/gaiad tx slashing unjail \ 
    --from=<key_name> \ 
    --chain-id=<chain_id> \ 
```

> Los validadores son encarcelados _(aka `jailed`)_, es decir, son retirados del conjunto de validadores activos, si no votan en 500 de los últimos 10.000 bloques, o si firman dos veces. Si te encarcelan por tiempo de inactividad, puedes devolver el poder de voto a tu validador.

### Gobernanza:

- Cómo subir una propuesta de gobernanza:
```sh
./gaia/build/gaiad tx gov submit-proposal \
    --title="Testing proposal" \
    --description="First tests with the governance module" \
    --type="Text" \
    --deposit="" \
    --from d1 \
    --chain-id="stargate-3a" \
```

`--title="Testing proposal"` => Título de nuestra proposición.

`--description="First tests with the governance module"` => Descripción de nuestra propuesta.

`--type="Text"` => Tipo de nuestra propuesta _(para más información sobre el tipo ver el [repositorio de Gavin en inglés](https://github.com/gavinly/CosmosParametersWiki/blob/master/Governance.md) o [en español](https://github.com/gavinly/CosmosParametersWiki/blob/master/Governance%5BES_es%5D.md))_.

`--deposit=""` => Depósito para que nuestra proposición entre en período de votación _(más info sobre `mindeposit` [en español](https://github.com/gavinly/CosmosParametersWiki/blob/master/Governance%5BES_es%5D.md#1-depositparams) o [en inglés](https://github.com/gavinly/CosmosParametersWiki/blob/master/Governance.md#1-depositparams))_. 

`--from d1` => Desde la wallet que se realiza la transacción.

`--chain-id="stargate-3a"` => Identificador único de cadena en la que se realiza la proposición.

### Cambios en sintaxis Stargate con respecto a Launchpad (Gobernanza):
* En la emisión del voto, cambia `no-with-veto` por `no_with_veto`
* Cuando usamos un fichero `json` para cargar una propuesta cambia también el esquema un poco en el campo `field` en el que se auna `amount` y `denom` para denominarse `deposit`:

```cat proposal2.json 
{
  "title": "Param-Change Voting Period",
  "description": "This is to change the voting time on Testnet to be 8 hours.",
  "changes": [
    {
      "subspace": "gov",
      "key": "votingparams",
      "value": {"voting_period":"28800000000000"}
     }
    ],
   "deposit":"10000000bcna"
}
```
El comando para enviar esta propuesta sería: 
```
bcnad tx gov submit-proposal param-change proposal2.json --from Pandora  --chain-id bitcanna ```




> _([FAQ](https://hub.cosmos.network/master/validators/validator-faq.html) de validadores)_
