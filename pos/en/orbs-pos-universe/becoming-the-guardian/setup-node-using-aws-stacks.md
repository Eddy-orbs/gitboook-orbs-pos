---
description: >-
  ⚠️ This is experimental and beta version. Only us-ease-1 region is supportive
  now.
---

# Setup node using AWS Stacks

Setup node using cloud prebuilt  (AWS Marketplace)

You can use cloud service such as Amazon Web Service to obtain node infrastructure and public IP address automated by marketplace product. This can be chosen by who does not have physical space to store computer and own public IP address or who is not familiar with remote terminal environments.

This step-by-step guide will walk you through creating a new node and connecting it to an existing Orbs network.

## Prerequisites

* Web browser (Chrome is recommended)
* AWS account
* AWS Elastic IP (Optional)
* Guardian Wallet (for stake/rewards) - HW wallet recommended
* A private key and public address of ERC20 address for node operation. For details see [below](setup-node-using-aws-stacks.md#allocate-orbs-node-address-and-private-key)
* Ethereum RPC endpoint url ([Infura](https://www.infura.io/) free tier is okay)

## Allocate Orbs Node address and private key

The Orbs Node address is a standard Ethereum Address. This address is used for (1) signing blocks on the Orbs network, and for (2) sending transactions to Orbs PoS smart contracts on Ethereum. Therefore, the Orbs node stores and uses the node address's private key. As such, the private key should be different from the Guardian private key.

During normal operation, Orbs node automatically sends transactions to the PoS smart contracts on Ethereum (e.g. to execute reward distribution or to signal that it is in sync with the network and ready to enter a committee). The Orbs Node address should hold enough ETH to fund the gas for transactions sent to the PoS contracts. It is the Guardian's responsibility to periodically verify the Orbs node address has a balance of at least 0.5 ETH.

Note - To complete registration, the Orbs node address is required to start with at least 1 ETH.

Orbs Node address and private key should be generated in a secure fashion and the private key is required during node deployment (see below).

The Orbs Node address is also required during the [Guardian Registration phase](register-your-guardian-on-chain.md).

The Orbs address may be modified, by updating the registration. Prior to a node address change, the node should be teared-down and redeployed.

_**The Orbs Node private key should be securely stored.**_

If needed, use [wallet generator](https://iancoleman.io/bip39/) to create new wallet provides privatekey.

## Launch Stack on AWS

1. login [AWS console website](https://console.aws.amazon.com/) using internet browser (ie. Chrome)
2. Shift + Click Launch button below:\
   [![Launch Stack us-east-1](https://camo.githubusercontent.com/45efe5b594d50802654c8f5ff0ab65e8e9c6ab7367f4d7cb4ac737a4ed4fdc75/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f4c61756e6368253230537461636b2d75732d2d656173742d2d312d6f72616e67653f7374796c653d666f722d7468652d6261646765266c6f676f3d616d617a6f6e617773)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=orbs-boyar-validator\&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct-autonet-github-us-east-1.yaml)

## **Quick create stack**

Input required items

1. Required Parameters
   1. **Stack name**: Any name you want to use (ie. `my-orbs-node-name`)
   2. **Ethereum RPC endpoint** (ie. `https://mainnet.infura.io/v3/06b6...b2d7e`)
   3. **Orbs node address without 0x** (ie. `246abE5D...000`)
   4. **Orbs private key without 0x** (ie. `0ab6F...000`)
   5. **SSH access CIDR**: input `0.0.0.0/0` or you can input as you want&#x20;
   6. **Node/status access CIDR**: modify to `0.0.0.0/0` or you can input as you want&#x20;
2. Optional Parameters
   1. Existing EIP AllocationId(Optional): input ip address when only you already have an allocated Elastic IP. If you don't have any EIP, just leave it empty. Tool will allocate automatically and return the address later.
   2. Optional EC2 Key Pair name(Optional): input key pair if you want to use SSH connection to installed server.
3. Click `Create stack` button
4.  Wait until `CREATE_COMPLETE` <br>

    <div align="left" data-with-frame="true"><figure><img src="../../.gitbook/assets/image (11).png" alt="" width="346"><figcaption></figcaption></figure></div>

## Backup outputs

<div align="left" data-with-frame="true"><figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure></div>

Goto Outputs tab on you installed CloudFormation Stack and backup your node information:

* **Elastic IP:** Allocated IPv4 address (ie. `44.207.238.247`)
* **BoyarStatusUrl:** http://\<your\_ip\_address>/services/boyar/status

## Verify your Node is deployed correctly

{% hint style="info" %}
Try to connect BoyarStatusUrl with browser and check that all the services started without errors
{% endhint %}

Check that all the services started without errors

```
http://<node ip>/services/boyar/status
```

Check your node's status in the PoS network (readings are 10 minutes delayed for finality)

```
http://<node ip>/services/management-service/status
```

## Register your guardian

To register on the network, go to [Guardian Registration](register-your-guardian-on-chain.md) (Requires Metamask)

## Stake ORBS

1. Transfer ORBS tokens from exchange or another source to your guardian wallet.
2. Transfer ETH for gas fee to approve required transaction during staking.
3. Connect wallet to [Tetra](https://staking.orbs.network/).&#x20;
4. Click "STAKE" button and follow the instruction.
   1. Approve token transfer to staking contract
   2. Execute staking contract function

{% hint style="info" %}
Unstake ORBS requires 14 days cooldown.
{% endhint %}
