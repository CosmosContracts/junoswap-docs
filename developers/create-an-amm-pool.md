---
description: General instructions on creating an AMM pool for use on the JunoSwap.com UI
cover: ../.gitbook/assets/JunoSwap gitbook 5 (1).png
coverY: 0
---

# Create an AMM Pool

JunoSwap AMM pools are created by interacting with the JunoSwap smart contract. These instructions will outline the arguments for your command and the procedure to successfully create your AMM pool on the JunoSwap smart contract.

An AMM pool can be created with any combination of native token, IBC coins and CW20 token. Developing CW20 token is outside the scope of this documentation. Refer to information [here](https://docs.junonetwork.io/smart-contracts-and-junod-development/tutorial-erc-20) to get started.

To create a new AMM pool, you will need to have the correct `denom` designations for your coins. In this example, we will be using native JUNO and IBC SCRT. The `denom` for these coins are:

| Symbol | Denom                                                                |
| ------ | -------------------------------------------------------------------- |
| JUNO   | ujuno                                                                |
| SCRT   | ibc/CD78EE5B20682E5A61B4D96C9F4DC39361269B88A6B3462C26A18652F7A90A9A |

You can find the `denom` of a token by querying your wallet address:

```bash
junod q bank balances <wallet-address>
```
If you're creating an AMM pool with a CW20 token, the `denom` message is the contract address, as such:

| Symbol | Denom                                                           |
| ------ | --------------------------------------------------------------- |
| JUNO   | ujuno                                                           |
| NETA   | juno168ctmpyppk90d34p3jjy658zf5a5l3w8wk35wht6ccqj4mr0yv8s4j5awr |

The procedure to create a new AMM pool is to:

1. [create the empty pool](create-an-amm-pool.md#create-empty-pool); and
2. [add liquidity](create-an-amm-pool.md#add-liquidity-to-amm-pool)

### Create empty pool

The wasm contract `Code ID` of the Junoswap contract is `16`. More information on wasm contracts [here](https://docs.cosmwasm.com/docs/1.0/). Each pool needs a unique label conforming to the standard `<token1> <token2> Pool`, where `<token1>` and `<token2>` are the two tokens in the pool.

To create an empty pool with native tokens we need to instantiate the wasm contract with our pool data. Using our `denom`'s identified earlier:

```bash
junod tx wasm instantiate 16 \
'{"token1_denom": {"native": "ujuno"},"token2_denom": {"native": "ibc/CD78EE5B20682E5A61B4D96C9F4DC39361269B88A6B3462C26A18652F7A90A9A"},"lp_token_code_id": 1}' \
--label "JUNO SCRT Pool" \
--gas 10000000 \
--fees 250000ujuno \
--admin juno1pv88fxyhvyv2edwed72leupsuympnwvmsny4jtzspv9zw2dcrhkseh58kn \
--from <wallet-name>
```

⚠**️Attention**, if your `token2_denom` is a token of type CW20, you must replace `native` by `cw20`.

```bash
junod tx wasm instantiate 16 \
'{"token1_denom": {"native": "ujuno"},"token2_denom": {"cw20": "juno1wc2qs0qy3t876g6ejphhcg3dre9084r2rwjmu2qg6d7v48welq5qxmcv79"},"lp_token_code_id": 1}' \
--label "JUNO XEZ Pool" \
--gas 10000000 \
--fees 250000ujuno \
--admin juno1pv88fxyhvyv2edwed72leupsuympnwvmsny4jtzspv9zw2dcrhkseh58kn \
--from <wallet-name>
```

In this command, `juno1pv88fxyhvyv2edwed72leupsuympnwvmsny4jtzspv9zw2dcrhkseh58kn` is the Raw Dao address 
which needs to be given admin rights to manage pool upgrades from the Dao.

{% hint style="info" %}
Replace \<wallet-name> with the name of your wallet in your local keystore.
{% endhint %}

If you query the `<transaction-hash>` resultant from the instantiate transaction:

```bash
junod q tx <transaction-hash> | jq -r .raw_log | jq .
```

{% hint style="info" %}
`<transaction-hash>` will look similar to `CA8A5EC4FB69B3DA95411212C9FDB9CF2A4D2128F6D3A15CFBA56136D760E023`
{% endhint %}

You should have a result similar to:

```json
[
  {
    "type": "instantiate",
    "attributes": [
      {
        "key": "_contract_address",
        "value": "juno1zz8vspvcq7a3t40sy6xfxa3yk8wuy3vr8clyn3pt8pthpucnvpyqes54md"
      },
      {
        "key": "code_id",
        "value": "16"
      },
      {
        "key": "_contract_address",
        "value": "juno1quce89l8clsn8s5tmq5sylg370h58xfnkwadx72crjv90jmetp4s3fkysl"
      },
      {
        "key": "code_id",
        "value": "1"
      }
    ]
  },
  {
    "type": "message",
    "attributes": [
      {
        "key": "action",
        "value": "/cosmwasm.wasm.v1.MsgInstantiateContract"
      },
      {
        "key": "module",
        "value": "wasm"
      },
      {
        "key": "sender",
        "value": "juno19w2488ntfgpduzqq3sk4j5x387zynwkn9rn2x3"
      }
    ]
  },
  {
    "type": "reply",
    "attributes": [
      {
        "key": "_contract_address",
        "value": "juno1zz8vspvcq7a3t40sy6xfxa3yk8wuy3vr8clyn3pt8pthpucnvpyqes54md"
      }
    ]
  }
]
```

If this is not successful, it is also possible to use https://dev.mintscan.io/juno/txs/6BAB0399B7A854E5CF03DEB940F87DF35B18C613ACF0AE92C9889B283B414ADE, where the transaction hash is replaced with your transaction hash. Click from parsing data to raw data, and obtain the data from there.

From this stdout you can extract the Junoswap contract address and the liquidity pool contract address. In this situaton, the data would be:

| <p>Junoswap </p><p>contract address</p>       | juno1zz8vspvcq7a3t40sy6xfxa3yk8wuy3vr8clyn3pt8pthpucnvpyqes54md |
| --------------------------------------------- | --------------------------------------------------------------- |
| <p>liquidity pool </p><p>contract address</p> | juno1quce89l8clsn8s5tmq5sylg370h58xfnkwadx72crjv90jmetp4s3fkysl |

### Approving contract to spend your tokens
If you're one or more of the tokens in the pool is a CW20 token, you will need to approve the Junoswap contract address to spend your tokens first. Use the following command:

```bash
junod tx wasm execute juno168ctmpyppk90d34p3jjy658zf5a5l3w8wk35wht6ccqj4mr0yv8s4j5awr '{"increase_allowance": {"amount": "100000000", "spender": "juno1zz8vspvcq7a3t40sy6xfxa3yk8wuy3vr8clyn3pt8pthpucnvpyqes54md"}}' \
--from <wallet-name> \
--gas 900000 \
--gas-prices 0.0025ujuno
```

In ths command, `juno168ctmpyppk90d34p3jjy658zf5a5l3w8wk35wht6ccqj4mr0yv8s4j5awr` is the token contract address, and `juno1zz8vspvcq7a3t40sy6xfxa3yk8wuy3vr8clyn3pt8pthpucnvpyqes54md` is the Junoswap contract address.

### Add liquidity to AMM pool

To add liquidity, you must first make sure you have funded your wallet with enough tokens to deposit into the AMM pool. In this example, you will be adding `250ujuno` and `1000uscrt`. Please note that you must use the correct `denom` for the tokens on chain.

Use the following command to send the `add_liquidity` message to our junoswap contract:

```bash
junod tx wasm execute juno1zz8vspvcq7a3t40sy6xfxa3yk8wuy3vr8clyn3pt8pthpucnvpyqes54md \
'{"add_liquidity": {"token1_amount": "250", "min_liquidity": "0", "max_token2": "1000"}}' \
--from <wallet-name> \
--gas 1500000 \
--amount 250ujuno,1000ibc/CD78EE5B20682E5A61B4D96C9F4DC39361269B88A6B3462C26A18652F7A90A9A \
--fees 37500ujuno
```

{% hint style="info" %}
Note that we have used the IBC `denom` for `uscrt` when sending the argument for the `--amount` flag.
{% endhint %}

Check the resultant transaction hash to ensure the transaction was successful:

```bash
junod q tx <transaction-hash>
```

Now that we have created and funded the AMM pool, we can [add it to the JunoSwap user interface](integrate-pool-to-junoswap-ui.md).
