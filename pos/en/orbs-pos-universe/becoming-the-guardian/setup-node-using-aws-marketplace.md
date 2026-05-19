---
description: ⚠️ This is experimental and beta version. Only 2 region is supportive now.
---

# Setup node using AWS marketplace

Setup node using cloud prebuilt  (AWS Marketplace)

You can use cloud service such as Amazon Web Service to obtain node infrastructure and public IP address automated by marketplace product. This can be chosen by who does not have physical space to store computer and own public IP address besides remote terminal environments.

This step-by-step guide will walk you through creating a new node and connecting it to an existing Orbs network.

## Prerequisites

* Remote pc (Windows, MacOS, Linux GUI)
* AWS account
* Guardian Wallet (stake, rewards) - HW wallet recommended
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

## Subscribe Orbs Node app from AWS marketplace

1. login AWS marketpalce website using internet browser (ie. Chrome)
2. Goto [Orbs Network Full Node](https://aws.amazon.com/marketplace/pp?sku=51c282skmep4qmamtmspsi8f3) application
3.  Click `View purchase option` <br>

    <div align="left" data-with-frame="true"><figure><img src="../../.gitbook/assets/image.png" alt="" width="563"><figcaption></figcaption></figure></div>
4. Scroll down and click `Subscribe`. This may take few minuates.
5.  Click `Launch your software`. <br>

    <div align="left" data-with-frame="true"><figure><img src="../../.gitbook/assets/image (1).png" alt="" width="506"><figcaption><p>You can see Launch button after subscription process done</p></figcaption></figure></div>

## Launch Orbs Network Full Node

{% hint style="info" %}
NOTE: during beta, only 2 regions are supportive. Please select one of regions in the dropdown menu.
{% endhint %}

1.  Select Region <br>

    <div align="left" data-with-frame="true"><figure><img src="../../.gitbook/assets/image (2).png" alt="" width="428"><figcaption></figcaption></figure></div>


2.  **Launch:** Click `Launch with CloudFormation` button

    <div align="left" data-with-frame="true"><figure><img src="../../.gitbook/assets/image (3).png" alt="" width="422"><figcaption></figcaption></figure></div>


3.  **Create Stack:** Click `Next` button. If inputs are empty, you can copy and paste:\
    Amazon S3 URL: `https://awsmp-cft-053155443450-1579814207723.s3.us-east-1.amazonaws.com/3b48d71a-feb8-4e18-87fb-27aafed9a2c0/3b48d71a-feb8-4e18-87fb-27aafed9a2c0/template-medium-ami-direct.yaml`

    <div align="left" data-with-frame="true"><figure><img src="../../.gitbook/assets/image (4).png" alt="" width="563"><figcaption></figcaption></figure></div>


4. **Specify stack details**: Input required items
   1. Stack name: Any name you want to use (ie. `my-orbs-node-name`)
   2. Ethereum RPC endpoint (ie. `https://mainnet.infura.io/v3/06b6...b2d7e`)
   3. Orbs node address without 0x (ie. `246abE5D...000`)
   4. Orbs private key without 0x (ie. `0ab6F...000`)
   5. Public access CIDR: modify to `0.0.0.0/0`
   6. Existing EIP AllocationId(Optional): input ip address when only you already have an allocated Elastic IP. If you don't have any EIP, just leave it empty. Tool will allocate automatically and return the address later.
   7. Optional EC2 Key Pair name(Optional): input key pair if you want to use SSH connection to installed server.
   8. Click `Next` button<br>
5. **Configure stack options:** Nothing to input. Click `Next` button.
6. **Review and create:** Scroll down and click `Submit` button.
7.  Wait until `CREATE_COMPLETE` <br>

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
