# SSV Scanner SDK

{% hint style="danger" %}
Please note: SSV Scanner SDK will soon be deprecated as it is becoming part of the [SSV SDK](../ssv-sdk/).
{% endhint %}

The SSV Scanner SDK enable users to retrieve events data from the SSV network [contract](https://docs.ssv.network/developers/smart-contracts).

The tool is used for retrieving **cluster snapshots** and **registration nonce** which are required as inputs for SSV tooling and smart contract transactions.

## Cluster Snapshots <a href="#ppf7v63kx7uq" id="ppf7v63kx7uq"></a>

Cluster snapshots are objects containing data of clusters in the SSV network.

Clusters are unique to each account and are defined by the combination of the validator’s **owner address** and **operator ids** which were selected to manage it during registration.

Cluster snapshots are required for SSV contract transactions with cluster related functions which require the **cluster object** as input:

* [registerValidator()](../smart-contracts/ssvnetwork.md#registervalidator-publickey-operatorids-shares-amount-cluster)
* [removeValidator()](../smart-contracts/ssvnetwork.md#removevalidator-publickey-operatorids-cluster)
* [deposit()](../smart-contracts/ssvnetwork.md#deposit-owner-operatorids-amount-cluster)
* [withdraw()](../smart-contracts/ssvnetwork.md#withdraw-operatorids-amount-cluster)
* [getBalance()](../smart-contracts/ssvnetworkviews.md#getbalance-owner-operatorids-cluster)
* [reactivate()](../smart-contracts/ssvnetwork.md#reactivate-operatorids-amount-cluster)
* [isLiquidated()](../smart-contracts/ssvnetworkviews.md#isliquidated-owner-operatorids-cluster)
* [isLiquidatable()](../smart-contracts/ssvnetworkviews.md#isliquidatable-owner-operatorids-cluster)
* [liquidate()](../smart-contracts/ssvnetwork.md#liquidate-owner-operatorids-cluster)

Cluster snapshots are updated after each transaction with a cluster related function, and will emit a new **cluster object** (the latest snapshot) which will be required for making the succeeding transaction.

The SSV Scanner tool returns only the **latest snapshot** for a provided cluster.

{% hint style="info" %}
Please note that alternatively, instead of using this tool, you could just save the **cluster object** emitted in the event after each transaction, and use it as the input for the succeeding cluster related contract transaction.
{% endhint %}

## Registration Nonce <a href="#x7nzjlwu00d0" id="x7nzjlwu00d0"></a>

The registration nonce is a sequence number that represents the number of validators an account (**owner address)** has registered to the SSV network.

The registration nonce increments after each validator registration and does not decrease when a validator is removed.

The registration nonce is required as input for the [SSV Keys](ssv-keys-distributor.md) tool, which outputs the **sharesData** payload required for the [validator registration](https://docs.ssv.network/developers/smart-contracts/ssvnetwork#public-registervalidator-publickey-operatorids-shares-amount-cluster) transaction in the SSV contract..

{% hint style="info" %}
Please note that alternatively, instead of using this tool to retrieve the **registration nonce**, you could just keep track of the number of validator registrations you have made and use it as the input for the succeeding validator registrations.
{% endhint %}

## Installation

The SSV Keys library is distributed as a Javascript package, and it can be directly installed with the `npm` command (or `yarn`, if you prefer) using the repository's URL:

```bash
npm i https://github.com/ssvlabs/ssv-scanner.git
```

{% hint style="info" %}
The library is not yet maintained in a stable manner on npmjs.com so for the time being, it is advised to use repository's URL, until this documentation page is updated to indicate otherwise.
{% endhint %}

## Import library

The two main components of the `ssv-scanner` library are the `ClusterScanner` and `NonceScanner` objects. Once the library has been installed, they can be imported like so:

```javascript
import { ClusterScanner, NonceScanner } from 'ssv-scanner';
```

## Usage

The usage of SSV scanner SDK is rather simple. Given a set of parameters, some generic (node URL, contract Address), some specific (owner address, operator IDs), the tool offers two objects a developer can interact with: `ClusterScanner` and `NonceScanner`.

`ClusterScanner` is able to extract the snapshot of the cluster, including the number of validators in it, information about the network fee, the operator's fees, its balance and if it's active or not.

`NonceScanner` can be used to verify how many times a certain address (the cluster owner) has registered a validator on SSV network, by invoking the `registerValidator()` and `bulkRegisterValidators()` functions of the SSV [smart contract](../smart-contracts/)  This counter is called "nonce".

## Example

```javascript
import { ClusterScanner, NonceScanner } from 'ssv-scanner';

async function main() {
  // these parameters should be known in advance
  const params = {
    nodeUrl: '', // this can be an Infura, or Alchemy node, necessary to query the blockchain
    contractAddress: '', // this is the address of SSV smart contract
    ownerAddress: '', // this is the wallet address of the cluster owner
    operatorIds: [], // this is a list of operator IDs chosen by the owner for their cluster
  }

  // ClusterScanner is initialized with the given parameters
  const clusterScanner = new ClusterScanner(params);
  // Return the Cluster Snapshot
  const result = await clusterScanner.run(params.operatorIds);
  console.log(JSON.stringify({
    'block': result.payload.Block,
    'cluster snapshot': result.cluster,
    'cluster': Object.values(result.cluster)
  }, null, '  '));

  // NonceScanner is initialized with the given parameters
  const nonceScanner = new NonceScanner(params);
  // Return the owner nonce
  const nextNonce = await nonceScanner.run();
  console.log('Next Nonce:', nextNonce);
}

void main();
```

Here's an example of the printed output:

```json
{
  "block": 9450944,
  "cluster snapshot": {
    "validatorCount": "1",
    "networkFeeIndex": "0",
    "index": "32010897120",
    "active": true,
    "balance": "5000000000000000000"
  },
  "cluster": [
    "1",
    "0",
    "32010897120",
    true,
    "5000000000000000000"
  ]
}
```
