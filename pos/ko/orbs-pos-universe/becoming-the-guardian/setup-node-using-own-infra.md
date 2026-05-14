# Setup node using own infra

## System Requirments

* CPU: Xeon series or higher recommended (equivalent to AWS r5.large spec)
* Memory: 16GB or more
* SSD: 256GB or more
* Network: 100Mbps or 1Gbps

## Prerequisites

* Hardware Server (see system requirements above) with network connection
* Ubuntu 20.04 or later LTS installed
* Public IP address (IPv4)
* Guardian Wallet (stake, rewards) - HW wallet recommended
* A private key of ERC20 address for node operation.
* Ethereum RPC endpoint url ([Infura](https://www.infura.io/) free tier is okay)
  * You can use your own rpc or another web3 service url
* Knowledge of the Linux terminal
* Allow ports 80, 7666, 9100, 10000-10100 (if you use ufw or firewall)

## Install tools

{% hint style="info" %}
use root account or use sudo for all terminal command&#x20;
{% endhint %}

### login root

```
sudo su
```

### Install docker

* follow instruction [here](https://docs.docker.com/engine/install/ubuntu/)
* make sure docker is running in swarm mode

### Install wget

```
apt-get install wget
```

### Install Boyar

{% hint style="info" %}
Boyar is the main orchestrator of the node. This is the first service to run and its purpose is to execute all the other services, provide their launch configuration, upgrade their software version, etc.
{% endhint %}

download boyar

```
wget https://github.com/orbs-network/boyarin/releases/download/v1.12.2/boyar-v1.12.2.bin
```

Confirm boyar checksum is the same as in here [binary download](https://github.com/orbs-network/boyarin/releases)

```
sha256sum boyar-v1.12.2.bin
```

Make executable

```
chmod +x boyar-v1.12.2.bin
```

Alias boyar

```
alias boyar=boyar-v1.12.2.bin
```

## Node configuration

### mgmt.json

Create `mgmt.json` file in the same directory where boyar is downloaded

```
vi mgmt.json
```

The content of the `mgmt.json` should be:

```
{
  "orchestrator": {
    "DynamicManagementConfig": {
      "Url": "http://localhost:7666/node/management",
      "ReadInterval": "1m",
      "ResetTimeout": "30m"
    },
    "storage-driver": "local",
    "storage-mount-type": "bind"
  },
  "services": {
    "management-service": {
      "InternalPort": 8080,
      "ExternalPort": 7666,
      "DockerConfig": {
        "Image": "orbsnetwork/management-service",
        "Tag": "bootstrap",
        "Pull": true
      },
      "Config": {
        "BootstrapMode": true,
        "EthereumEndpoint": "<ETHEREUM_ENDPOINT>",
        "DockerNamespace": "orbsnetwork"
      }
    }
  }
}
```

Paste and edit:

* replace `<ETHEREUM_ENDPOINT>` with URL of Ethereum RPC endpoint

### keys.json

Create `mgmt.json` file in the same directory where boyar is downloaded

```
vi keys.json
```

The content of the `keys.json` should be:

```
{
  "node-address": "<NODE_ADDRESS_WITH_NO_LEADING_0x>",
  "node-private-key": "<PRIVATE_KEY>"
}
```

Paste and edit:

* replace `<NODE_ADDRESS_WITH_NO_LEADING_0x>` with node operation address without `0x` header\
  ie. `4aca0c63e351b2ea44ee628425710e933b5b3396`
* replace `<PRIVATE_KEY>` with node operation address without `0x` header\
  ie. `f5f83Ee70a85fFF2exxxxxxxxxxxxxxxxxxxxxxxxxxx334932F34C8D629165Ed`

### Create directories

```
mkdir -p /var/efs/ /var/efs/boyar-status /var/efs/boyar-logs
```

## Run Boyar

[Boyar](https://github.com/orbs-network/boyarin) is a bootstrapping software that we use to provision virtual layers. First, it starts a management service container that pulls info from Ethereum, including the list of the chains, consensus committees, and so on, and then uses this information to run other containers.

```
vi run_boyar.sh
```

paste boyar command&#x20;

```
boyar --keys ./keys.json --management-config ./mgmt.json --log /var/efs/boyar-logs/current --status /var/efs/boyar-status/status.json --bootstrap-reset-timeout 30m --auto-update --shutdown-after-update
```

Make executable

```
chmod +x run_boyar.sh
```

Run Boyar

```
run_boyar.sh
```

Boyar is also responsible for upgrading and supervising modules in the node. To ensure it is constantly running, and to enable self upgrading of Boyar itself, an external process manager is required.

For example, [Supervisord](https://github.com/Supervisor/supervisor):

## Supervisord

### Supervisord config example

Place in `/opt/orbs/boyar.sh`

```
#!/bin/bash

trap "kill -- -$$" EXIT

multilog_err=1
multilog_cmd="multilog s16777215 n32 /var/efs/boyar-logs/"

while [[ "$multilog_err" -ne "0" ]]; do
    sleep 1
    echo "boyar logging pre checks..." | $multilog_cmd
    multilog_err=$?
done

echo "Running boyar..."

# FIXME: please set correct /path/to/keys.json
# FIXME: please set correct /path/to/mgmt.json

exec /usr/bin/boyar --keys /path/to/keys.json --max-reload-time-delay 0m --bootstrap-reset-timeout 30m --status /var/efs/boyar-status/status.json  --management-config /path/to/management-config.json --auto-update --shutdown-after-update 2>&1 | $multilog_cmd
```

Place in `/etc/supervisor/conf.d/boyar.conf`

```
[program:boyar]
command=/opt/orbs/boyar.sh
autostart=true
autorestart=true
environment=HOME="/root"
stdout_logfile=/var/efs/boyar-logs/supervisor.stdout
redirect_stderr=true
stdout_logfile_maxbytes=10MB
```

### Systemd unit config example

```
[Unit]
Description=Boyar service
After=network.target

[Service]
# FIXME: please set correct /path/to/keys.json
# FIXME: please set correct /path/to/mgmt.json
ExecStart=/usr/bin/boyar --keys /path/to/keys.json --management-config /path/to/mgmt.json --log /var/efs/boyar-logs/current --status /var/efs/boyar-status/status.json --auto-update --shutdown-after-update --bootstrap-reset-timeout 30m
Restart=always
KillSignal=SIGHUP

[Install]
WantedBy=default.target
```

## Prometheus node-exporter (optional)

create a shell file and execute

```
curl -L https://github.com/prometheus/node_exporter/releases/download/v${var.node_exporter_version}/node_exporter-${var.node_exporter_version}.linux-amd64.tar.gz -o /home/ubuntu/node_exporter.tar.gz
cd /home/ubuntu
tar xvfz node_exporter.tar.gz && mv node_exporter-0.18.1.linux-amd64/node_exporter .
chmod +x node_exporter
rm -f node_exporter.tar.gz

echo "[program:node_exporter]
command=/home/ubuntu/node_exporter --collector.ntp --collector.tcpstat --collector.supervisord
autostart=true
autorestart=true
stderr_logfile=/var/log/node_exporter.err.log
stdout_logfile=/var/log/node_exporter.log" >> /etc/supervisor/conf.d/node_exporter.conf

supervisorctl reread && supervisorctl update
```

## Verifying your node's health

From your Linux machine you can access these URLs:

* [http://localhost/services/boyar/status](http://localhost/services/boyar/status)
* [http://localhost/services/boyar/logs](http://localhost/services/boyar/logs)
* [http://localhost/services/management-service/status](http://localhost/services/management-service/status)
* [http://localhost/services/management-service/logs](http://localhost/services/management-service/logs)

You can also replace `localhost` with your node IP address.

## Register your guardian

To register on the network, go to [Guardian Registration](register-your-guardian-on-chain.md) (Requires Metamask)\
<br>
