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
```sh
git clone https://github.com/cosmos/gaia.git && cd gaia

git checkout v4.0.4

make install
```

### Restauramos las wallets de la Ledger:
```sh
gaiad keys add d1 --ledger
```

>`1000000uatom=1atom`

### Delegar *(si tenéis fondos en staking con DelegaNetworks preguntadnos por nuestro nodo)*:
```sh
gaiad tx staking delegate cosmosvaloper1uutuwrwt3z2a5z8z3uasml3rftlpmu25aga5c6 1000000uatom --from TUWALLETAQUÍ --chain-id cosmoshub-4 --fees 5000uatom --node <IP>:<PUERTO> -y
```

### Comprobar si tenemos rewards:
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