# cosmos-rest-faucet
A REST server for dispensing testnet tokens

## Features

- Responds to requests for tokens on multiple testnets
- Response includes a link to the transaction detail in the appropriate block explorer
- Limits the tokens a user can get within a time period for a given testnet
- Limits the tokens an address can get within a time period for a given testnet
- Daily cap for each testnet token
- Requests are saved in local csv file: date, cosmos address, amount, and testnet
- Errors are logged to systemd journal

## Requirements

- python 3.8.12+
- gaia v7.0.0+
- Initialized gaia instance
- Faucet keys in gaia keyring

## Installation

1. Python dependencies:
   
```
cosmos-rest-faucet$ python -m venv .env
cosmos-rest-faucet$ source .env/bin/activate
cosmos-rest-faucet$ pip install -r requirements.txt
```

- Modify the nodes, faucet addresses, amount to send, etc. in `config.toml`

## Usage

This bot can be run stand-alone (mostly for testing), or as a service.

### Stand-alone

```
hypercorn cosmos_rest_faucet:app
```

- This can be run inside a `tmux` session.

### Service

1. Modify the `cosmos-rest-faucet.service` file as appropriate.
2. Make a copy of `cosmos-rest-faucet.service` or create a link to it in `/etc/systemd/system/`.
3. Enable and start the service:
```
systemctl daemon-reload
systemctl enable cosmos-rest-faucet.service
systemctl start cosmos-rest-faucet.service
systemctl status cosmos-rest-faucet.service
```

## API

1. Request tokens:  

`/request?address=<cosmos_address>&chain=<chain_id>`

The chain must match one of the testnet entries in `config.toml`.

The response will be a JSON message:

```
{
  "address": <cosmos_address,
  "amount": <tokens_sent>,
  "chain": <chain_id,
  "hash": <hash_id>,
  "status": <"success" or "fail">
}
```

2. Request a balance:  

`/balance?address=<cosmos_address>&chain=<chain_id>`

The chain must match one of the testnet entries in `config.toml`.

The response will be a JSON message:

```
{
  "address": <cosmos_address>,
  "balance": [
    {
      "amount": <amount_1>,
      "denom": <denom_1>
    },
    ...
    {
      "amount": "amount_n",
      "denom": <denom_n>
    }
  ],
  "chain": <chain_id>,
  "status": <"success" or "fail">
}
```