---
description: ⚠️ This is experimental and beta version.
---

# Setup node using AWS Marketplace

Setup node using cloud prebuilt image (AWS Marketplace).

You can use cloud service such as Amazon Web Service to obtain node infrastructure and public IP address automated by marketplace product. This can be chosen by who does not have physical space to store computer and own public IP address or who is not familiar with remote terminal environments.

This step-by-step guide will walk you through creating a new node and connecting it to an existing Orbs network.

## Prerequisites

* Web browser (Chrome is recommended)
* AWS account
* AWS Elastic IP (Optional)
* Guardian Wallet (for stake/rewards) - HW wallet recommended
* A private key and public address of ERC20 address for node operation. For details see [below](setup-node-using-aws-marketplace.md#allocate-orbs-node-address-and-private-key)
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

## Subscribe Orbs Node app from AWS marketplace <a href="#subscribe-orbs-node-app-from-aws-marketplace" id="subscribe-orbs-node-app-from-aws-marketplace"></a>

1. login [AWS marketpalce website](https://aws.amazon.com/marketplace) using internet browser (ie. Chrome)
2. Goto [Orbs Network Full Node](https://aws.amazon.com/marketplace/pp?sku=51c282skmep4qmamtmspsi8f3) application
3.  Click `View purchase option`&#x20;

    <div align="left" data-with-frame="true"><img src="https://orbs-doc.gitbook.io/pos/~gitbook/image?url=https%3A%2F%2F3545397290-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252Fg5oTTPwJkXaserapTxr7%252Fuploads%252FP1ay9DhcpCc5oru9wXTz%252Fimage.png%3Falt%3Dmedia%26token%3Dfd09be62-5aff-4f8c-b3c6-4d641cb28c29&#x26;width=768&#x26;dpr=3&#x26;quality=100&#x26;sign=405529d8&#x26;sv=2" alt="" width="563"></div>


4. Scroll down and click `Subscribe`. This may take few minuates.
5.  Click `Launch your software`.

    <div align="left" data-with-frame="true"><figure><img src="../../.gitbook/assets/image (13).png" alt="You can see Launch button after subscription process done" width="506"><figcaption><p>You can see Launch button after subscription process done</p></figcaption></figure></div>

## Launch Orbs Network Full Node <a href="#launch-orbs-network-full-node" id="launch-orbs-network-full-node"></a>

1.  Select Region

    <div align="left" data-with-frame="true"><img src="https://orbs-doc.gitbook.io/pos/~gitbook/image?url=https%3A%2F%2F3545397290-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252Fg5oTTPwJkXaserapTxr7%252Fuploads%252Fz6W76sFpWiFgTdZfyUjF%252Fimage.png%3Falt%3Dmedia%26token%3Dcf5f56db-cc22-45d2-a1d0-a13102afd44d&#x26;width=768&#x26;dpr=3&#x26;quality=100&#x26;sign=4bfc9428&#x26;sv=2" alt="" height="79" width="570"></div>


2.  **Launch:** Click `Launch with CloudFormation` button

    <div align="left" data-with-frame="true"><img src="https://orbs-doc.gitbook.io/pos/~gitbook/image?url=https%3A%2F%2F3545397290-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252Fg5oTTPwJkXaserapTxr7%252Fuploads%252FaRD8LVmIC4ggexxAmmIq%252Fimage.png%3Falt%3Dmedia%26token%3D10cbd8e7-3fb9-41be-9862-3e07b792f03c&#x26;width=768&#x26;dpr=3&#x26;quality=100&#x26;sign=b228540d&#x26;sv=2" alt="" height="230" width="563"></div>


3.  **Create Stack:** Click `Next` button. If S3 URL is empty, you can copy and paste: \
    Amazon S3 URL: `https://awsmp-cft-053155443450-1579814207723.s3.us-east-1.amazonaws.com/419ae877-2d01-4c05-84c8-d20e52986770/419ae877-2d01-4c05-84c8-d20e52986770/template-medium-ami-direct-autonet.yaml`&#x20;

    <div align="left" data-with-frame="true"><img src="https://orbs-doc.gitbook.io/pos/~gitbook/image?url=https%3A%2F%2F3545397290-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252Fg5oTTPwJkXaserapTxr7%252Fuploads%252F51dkM17VaOqCmr6FdcVJ%252Fimage.png%3Falt%3Dmedia%26token%3Da7453afe-1a6b-4be2-9a6e-64d44bee8210&#x26;width=768&#x26;dpr=3&#x26;quality=100&#x26;sign=77d45704&#x26;sv=2" alt=""></div>


4. **Specify stack details**: Input required items
   1. Required Parameters
      1. **Stack name**: Any name you want to use (ie. `my-orbs-node-name`)
      2. **Ethereum RPC endpoint** (ie. `https://mainnet.infura.io/v3/06b6...b2d7e`)
      3. **Orbs node address without 0x** (ie. `246abE5D...000`)
      4. **Orbs private key without 0x** (ie. `0ab6F...000`)
      5. **SSH access CIDR**: input `0.0.0.0/0` or you can input as you want
      6. **Node/status access CIDR**: modify to `0.0.0.0/0` or you can input as you want
   2. Optional Parameters
      1. Existing EIP AllocationId(Optional): input ip address when only you already have an allocated Elastic IP. If you don't have any EIP, just leave it empty. Tool will allocate automatically and return the address later. We recommend allocate EIP manually and keepp it. Because re-install cause change of ip address.
      2. Optional EC2 Key Pair name(Optional): input key pair if you want to use SSH connection to installed server.
   3. Click `Next` button
5. **Configure stack options:** Nothing to input. Click `Next` button.
6. **Review and create:** Scroll down and click `Submit` button.
7.  Wait until `CREATE_COMPLETE`

    <div align="left" data-with-frame="true"><img src="https://orbs-doc.gitbook.io/pos/~gitbook/image?url=https%3A%2F%2F3545397290-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252Fg5oTTPwJkXaserapTxr7%252Fuploads%252FyKOOAmg6JzR3UCgKtI5P%252Fimage.png%3Falt%3Dmedia%26token%3D3eb78c65-b6dd-476c-8450-61114af76fce&#x26;width=768&#x26;dpr=3&#x26;quality=100&#x26;sign=35771510&#x26;sv=2" alt="" height="371" width="461"></div>

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
