# Operator Onboarding

### **Operator Onboarding**

To join the network as an operator a user must run an SSV node.&#x20;

This is software containing the SSV protocol implementation and integration to the network’s smart contracts.

This can be done by installing the [node software](../../operator-user-guides/operator-node/installation.md) on a machine followed by registering the operator either through the [smart contract](../../developers/smart-contracts/ssvnetwork.md#public-registeroperator-publickey-operatorfee) or [webapp](https://app.ssv.network/) interface.

#### Operator Fee

Operators set their own fees - denominated in SSV tokens - to be charged per each validator that selects them as one of their operators.

Operator earnings are paid to their account balance, and can be withdrawn to their wallet at any time.

**Fee Configuration**

Operators initially set their fee when registering an operator to the network.

Fees are presented as annual payments, but in practice are paid to operators continuously as an ongoing process - per each passed block.

This means that when setting the operator fee through the smart contract (whether at registration or fee updates), operators should set their preferred fee according to a fee per block format.

* To calculate fee per block according to a desired annual fee in fiat (USD):

$$
Fee\;per\;block = \frac{Annuel\;Fee_{usd}}{SSV_{usd}}\;/\;Blocks_{year}
$$

* Legend
  * $$Annual\;Fee_{usd}$$ - desired fee per year in USD
  * $$SSV_{usd}$$ - $SSV price in USD
  * $$Blocks_{year}$$ - avg. number of blocks per year ([reference](https://ycharts.com/indicators/ethereum_blocks_per_day))
