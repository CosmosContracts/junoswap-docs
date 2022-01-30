---
description: General instructions on creating an AMM pool for use on the JunoSwap.com UI
cover: ../.gitbook/assets/JunoSwap gitbook 5 (1).png
coverY: 0
---

# Create an AMM Pool

JunoSwap AMM pools are created by interacting with the JunoSwap smart contract. These instructions will outline the arguments for your command and the procedure to successfully create your AMM pool on the JunoSwap smart contract.

An AMM pool can be created with any combination of native token, IBC coins and CW20 coins. Developing CW20 coins is outside the scope of this documentation. Refer to information [here](https://docs.junonetwork.io/smart-contracts-and-junod-development/tutorial-erc-20) to get started.

To create a new AMM pool, you will need to have the correct `denom` designations for your coins. In this example, we will be using native JUNO and IBC SCRT. The `denom` for these coins are:

| Symbol | Denom                                                                |
| ------ | -------------------------------------------------------------------- |
| JUNO   | ujuno                                                                |
| SCRT   | ibc/CD78EE5B20682E5A61B4D96C9F4DC39361269B88A6B3462C26A18652F7A90A9A |

You can find the `denom` of a token by querying your wallet address:

```bash
junod q bank balances <wallet-address>
```

The procedure to create a new AMM pool is to:

1. [create the empty pool](create-an-amm-pool.md#create-empty-pool); and
2. [add liquidity](create-an-amm-pool.md#add-liquidity-to-amm-pool)

### Create empty pool

The wasm contract `Code ID` of the junoswap contract is `16`. More information on wasm contracts [here](https://docs.cosmwasm.com/docs/1.0/).

To create an empty pool we need to instantiate the wasm contract with our pool data. Using our `denom`'s identified earlier:

```bash
junod tx wasm instantiate 16 \
'{"token1_denom": {"native": "ujuno"},"token2_denom": {"native": "ibc/CD78EE5B20682E5A61B4D96C9F4DC39361269B88A6B3462C26A18652F7A90A9A"},"lp_token_code_id": 1}' \
--label "JUNO SCRT Pool" \
--gas 10000000 \
--fees 250000ujuno \
--from <wallet-name>
```

{% hint style="info" %}
Replace \<wallet-name> with the name of your wallet in your local keystore.
{% endhint %}

If we query the `<transaction-hash>` resultant from the instantiate transaction:

```bash
junod q tx <transaction-hash> | jq -r .raw_log | jq .
```

{% hint style="info" %}
`<transaction-hash>` will look similar to `CA8A5EC4FB69B3DA95411212C9FDB9CF2A4D2128F6D3A15CFBA56136D760E023`
{% endhint %}

We will have a result similar to:

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

From this stdout we can extract the junoswap contract address and the liquidity pool contract address. In this case:

| <p>junoswap </p><p>contract address</p>       | juno1zz8vspvcq7a3t40sy6xfxa3yk8wuy3vr8clyn3pt8pthpucnvpyqes54md |
| --------------------------------------------- | --------------------------------------------------------------- |
| <p>liquidity pool </p><p>contract address</p> | juno1quce89l8clsn8s5tmq5sylg370h58xfnkwadx72crjv90jmetp4s3fkysl |

### Add liquidity to AMM pool

To add liquidity, we must first  make sure we have funded our wallet with enough token to deposit into the AMM pool. in this example we will be adding `250ujuno` and `1000uscrt`. Please note that we must use the correct `denom` for the tokens on chain.

We will use the following command to send the `add_liquidity` message to our junoswap contract:

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
