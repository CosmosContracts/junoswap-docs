---
description: General instructions for integration of your AMM pool into the JunoSwap.com UI
cover: ../.gitbook/assets/JunoSwap gitbook 5 (3).png
coverY: 0
---

# Integrate Pool to JunoSwap UI

To enable your pool to be visible and useable on the junoswap.com user interface, you must:

1. create formatted JSON according to the template provided;
2. fork the junoswap-asset-list repository on GitHub;
3. add your pool data to token\_list.json; and
4. submit a PR to be assessed and merged.

Please carefully follow these instructions so that your pull request is not rejected.

### Create JSON for integration

The following example JSON is to integrate native JUNO and IBC SCRT pool into the junoswap.com user interface.

You will need to use the `swap_address` that was returned when you created your AMM pool. See [here](create-an-amm-pool.md) for instructions.

You must ensure that your `pool_id` is unique and formatted correctly or your pull request will be rejected. Please use the dominant token first, followed by `-` and then your secondary token.

```json
{
      "id": "secret",
      "pool_id": "JUNO-SCRT",
      "chain_id": "secret-4",
      "token_address": "",
      "swap_address": "juno1zz8vspvcq7a3t40sy6xfxa3yk8wuy3vr8clyn3pt8pthpucnvpyqes54md",
      "symbol": "SCRT",
      "name": "SCRT",
      "decimals": 6,
      "logoURI": "https://raw.githubusercontent.com/osmosis-labs/assetlists/main/images/scrt.png",
      "tags": ["native"],
      "native":true,
      "ibc/CD78EE5B20682E5A61B4D96C9F4DC39361269B88A6B3462C26A18652F7A90A9A"
    }
```

{% hint style="info" %}
`pool_id`: unique id

`chain_id`: source chain id or juno-1 if token

`token address`: cw20 token address or empty if native

`swap address`: swap contract address
{% endhint %}

### Fork the assets GitHub repo and add json

You will need to fork the junoswap-asset-list repository:

{% embed url="https://github.com/CosmosContracts/junoswap-asset-list" %}

After forking the repository, locate the asset list: `junoswap-asset-list/token_list.json`

Edit this file. Scroll to the bottom of the list and add your JSON to the end of the `tokens` array in the same manor as all the other pool entries. Please ensure you ad a `,` to the preceding JSON closing curly brace.

Submit a PR with your changes with the following name:

`Add <pool_id>`&#x20;

{% hint style="info" %}
Replace `<pool_id>` with the one used in your JSON.
{% endhint %}

Monitor your pull request and answer any questions from the team, and make modifications as requested.
