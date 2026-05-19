# Decommissioning node

## Withdraw your assets

### Unstake Staked ORBS

Deregistered guardian will not get staking rewards anymore. Unstaking and withdrawal ORBS is required. You can unstake the asset after unregistration.

### Withdraw deposit ETH/POL

You can transfer ETH from the node address. If your node is on polygon network, you need to check POL in node address, too.

## Unregister the guardian

To unregister on the network, go to [ORBS Guardians Portal](https://guardians.orbs.network/registration) (Requires Metamask connection)

1. Open [Guardians Portal ](https://guardians.orbs.network/)on Chrome browser
2. Connect Metamask(Guardian wallet) and process UNREGISTER

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

⚠︎ NOTE:&#x20;

* If you are also registered on polygon network, repeat unregister process after changing network to poloygon
* If you want to execute the contract manually, run [this contract function](https://etherscan.io/address/0xce97f8c79228c53b8b9ad86800a493d1e7e5d1e3#writeContract#F13)

## Uninstall deployed service

### Node using cloud (AWS)

1. Open terminal on your remote computer and change directory to where [`orbs-node.json`](#user-content-fn-1)[^1] file located.
2. run polygon destroy command

```
polygon destroy -f orbs-node.json
```

3. Deallocate "Elastic IP" at AWS console.

If you failed to destroy node remotely, you can terminate EC2 service at AWS console.

### Node using own infra

Kill boyar process

### Node using AWS marketplace

1. Login [AWS console](https://console.aws.amazon.com/console/home/?nc2=h_si\&src=header-signin)
2. Goto `CloudFormation` service&#x20;
3. Select your node from `Stacks` list
4. Click `Delete stack` button

## Verify your Node is destroyed correctly

Check that all the services is out of connection.

```
http://<node ip>
```

Check your node is disappeared from the [node status list](https://status.orbs.network/).

[^1]: 
