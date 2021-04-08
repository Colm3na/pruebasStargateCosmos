# Información relacionada con los delegadores.

> El sistema operativo usado en las pruebas es Ubuntu.

## Añadimos dependencias:
```sh
sudo apt install -y git make gcc jq build-essential
```

> Necesitamos tener la versión de Go `go1.15.6`

## Instalamos Go:
```sh
wget -c 'https://dl.google.com/go/go1.15.6.linux-amd64.tar.gz' -O go1.15.6.linux-amd64.tar.gz

sudo tar -C /usr/local/ -xzf go1.15.6.linux-amd64.tar.gz

sudo rm -Rf go1.15.6.linux-amd64.tar.gz
```

### Añadimos al `.bashrc` o al `.profile`
```sh
#Go:
export PATH="$PATH:/usr/local/go/bin"
export GOPATH="$HOME/go"
export PATH="$PATH:$GOROOT/bin:$GOPATH/bin"
export GOBIN="$GOPATH/bin"
```

>Recargamos nuestra terminal con `source .bashrc` o `source .profile`

## Instalamos gaia:

> Comprobad siempre la última versión [en su repositorio](https://github.com/cosmos/gaia/releases).

```sh
git clone https://github.com/cosmos/gaia.git && cd gaia

git checkout v4.2.0

make install
```

> Comprobamos la versión de `gaia` instalada:
```sh
gaiad version --long | grep version
```

> Debería darnos un resultado parecido a este:
```
version: v4.2.0
go: go version go1.15.6 linux/amd64
```

### Restauramos las wallets de la Ledger:
```sh
gaiad keys add d1 --ledger
```

> Recordad que `1000000uatom=1atom`.

### Delegar *(si tenéis fondos en staking con DelegaNetworks preguntadnos por nuestro nodo)*. `cosmosvaloper1uut...` es el nodo de DelegaNetworks, cambiadlo por vuestro validador:
```sh
gaiad tx staking delegate cosmosvaloper1uutuwrwt3z2a5z8z3uasml3rftlpmu25aga5c6 1000000uatom --from TUWALLETAQUÍ --chain-id cosmoshub-4 --fees 5000uatom --node <IP>:<PUERTO> -y
```

### Comprobar si tenemos rewards, como en el caso anterior `cosmosvaloper1uut...` es el validador de DelegaNetworks, y `cosmos1uutu` es la wallet desde la que queremos comprobar si tenemos rewards:
```sh
gaiad query distribution rewards cosmos1uutuwrwt3z2a5z8z3uasml3rftlpmu25cufp5f cosmosvaloper1uutuwrwt3z2a5z8z3uasml3rftlpmu25aga5c6 --node <IP>:<PUERTO> --chain-id cosmoshub-4
```

### Reclamar rewards desde Ledger:
```sh
gaiad tx distribution withdraw-all-rewards --from d1 --node <IP>:<PUERTO> --chain-id cosmoshub-4 --fees 5000uatom
```

### Delegar desde Ledger:
```sh
gaiad tx staking delegate cosmosvaloper1uutuwrwt3z2a5z8z3uasml3rftlpmu25aga5c6 20000000uatom --from d1 --node <IP>:<PUERTO> --fees 5000uatom --chain-id cosmoshub-4
```

### Enviar desde la Ledger:
```sh
gaiad tx bank send d1 cosmos1uutuwrwt3z2a5z8z3uasml3rftlpmu25cufp5f 1000000uatom --fees 5000uatom --chain-id cosmoshub-4 --node <IP>:<PUERTO>
```

### Votar en las propuestas de gobernanza:

- Comprobamos las propuestas que actualmente hay en proceso de votación:
```sh
gaiad query gov proposals --status voting_period --chain-id cosmoshub-4 --node <IP>:<PUERTO>
```

> Las opciones para el voto son `Yes-No-NoWithVeto-Abstain`.

- Realizamos la votación, suponemos que el ID de la propuesta es `X` y votamos `Yes` desde la wallet `d1`:
```sh
gaiad tx gov vote X Yes --from d1 --chain-id cosmoshub-4 --node <IP>:<PUERTO> --fees=5000uatom
```
