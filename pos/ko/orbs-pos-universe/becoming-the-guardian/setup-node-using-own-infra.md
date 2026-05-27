# 노드 설치 방법 (자체 서버 이용)

## 시스템 요구 사항

* CPU: Xeon급 이상 권장(AWS r5.large 사양에 대응)
* 메모리: 16GB 이상
* SSD: 256GB 이상
* 네트워크: 100Mbps 또는 1Gbps

## 사전 준비

* 위 시스템 요구를 갖춘 하드웨어 서버(네트워크 연결 상태)
* Ubuntu 20.04 이상 LTS 설치
* 공인 IP 주소(IPv4)
* 가디언 지갑(스테이킹, 보상) — 하드웨어 지갑 권장
* 노드 운용용 ERC20 주소의 개인키
* 이더리움 RPC 엔드포인트 URL([Infura](https://www.infura.io/) 무료 티어 가능)
  * 자체 RPC 또는 다른 web3 서비스 URL 사용 가능
* Linux 터미널 사용에 익숙할 것
* 포트 80, 7666, 9100, 10000-10100 허용(ufw 등 방화벽 사용 시)

## 도구 설치

{% hint style="info" %}
root 계정을 쓰거나 모든 터미널 명령에 sudo를 사용하세요.
{% endhint %}

### root 로그인

```
sudo su
```

### Docker 설치

* [여기](https://docs.docker.com/engine/install/ubuntu/) 안내를 따릅니다.
* Docker가 swarm 모드로 동작하는지 확인합니다.

### wget 설치

```
apt-get install wget
```

### Boyar 설치

{% hint style="info" %}
Boyar는 노드의 주 오케스트레이터입니다. 가장 먼저 실행되는 서비스로, 다른 서비스를 실행하고 기동 설정을 제공하며 소프트웨어 버전 업그레이드 등을 담당합니다.
{% endhint %}

Boyar 다운로드

```
wget https://github.com/orbs-network/boyarin/releases/download/v1.12.2/boyar-v1.12.2.bin
```

Boyar 체크섬이 [바이너리 다운로드](https://github.com/orbs-network/boyarin/releases) 페이지와 동일한지 확인합니다.

```
sha256sum boyar-v1.12.2.bin
```

실행 권한 부여

```
chmod +x boyar-v1.12.2.bin
```

Boyar 별칭

```
alias boyar=boyar-v1.12.2.bin
```

## 노드 설정

### mgmt.json

Boyar를 받은 디렉터리와 같은 위치에 `mgmt.json` 파일을 만듭니다.

```
vi mgmt.json
```

`mgmt.json` 내용은 다음과 같아야 합니다.

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

붙여넣고 수정:

* `<ETHEREUM_ENDPOINT>`를 이더리움 RPC 엔드포인트 URL로 바꿉니다.

### keys.json

Boyar를 받은 디렉터리와 같은 위치에 `keys.json` 파일을 만듭니다.

```
vi keys.json
```

`keys.json` 내용은 다음과 같아야 합니다.

```
{
  "node-address": "<NODE_ADDRESS_WITH_NO_LEADING_0x>",
  "node-private-key": "<PRIVATE_KEY>"
}
```

붙여넣고 수정:

* `<NODE_ADDRESS_WITH_NO_LEADING_0x>`를 앞에 `0x` 없는 노드 운용 주소로 바꿉니다.\
  예: `4aca0c63e351b2ea44ee628425710e933b5b3396`
* `<PRIVATE_KEY>`를 앞에 `0x` 없는 노드 운용 개인키로 바꿉니다.\
  예: `f5f83Ee70a85fFF2exxxxxxxxxxxxxxxxxxxxxxxxxxx334932F34C8D629165Ed`

### 디렉터리 생성

```
mkdir -p /var/efs/ /var/efs/boyar-status /var/efs/boyar-logs
```

## Boyar 실행

[Boyar](https://github.com/orbs-network/boyarin)는 가상 레이어 서비스를 제공하는 데 쓰는 필수 소프트웨어입니다. 먼저 이더리움에서 체인 목록, 합의 위원회 등 정보를 가져오는 management 서비스 컨테이너를 기동한 뒤, 그 정보로 다른 컨테이너를 실행합니다.

```
vi run_boyar.sh
```

boyar 명령을 붙여넣습니다.

```
boyar --keys ./keys.json --management-config ./mgmt.json --log /var/efs/boyar-logs/current --status /var/efs/boyar-status/status.json --bootstrap-reset-timeout 30m --auto-update --shutdown-after-update
```

실행 권한 부여

```
chmod +x run_boyar.sh
```

Boyar 실행

```
run_boyar.sh
```

Boyar는 노드 모듈 업그레이드와 감시도 담당합니다. 항상 실행되게 하고 Boyar 자체도 스스로 업그레이드하려면 외부 프로세스 관리자가 필요합니다.

예: [Supervisord](https://github.com/Supervisor/supervisor)

## Supervisord

### Supervisord 설정 예시

`/opt/orbs/boyar.sh`에 둡니다.

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

`/etc/supervisor/conf.d/boyar.conf`에 둡니다.

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

### Systemd 유닛 설정 예시

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

## Prometheus node-exporter(선택)

쉘 스크립트를 만들고 실행합니다.

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

## 노드 상태 확인

Linux 머신에서 다음 URL에 접근할 수 있습니다.

* [http://localhost/services/boyar/status](http://localhost/services/boyar/status)
* [http://localhost/services/boyar/logs](http://localhost/services/boyar/logs)
* [http://localhost/services/management-service/status](http://localhost/services/management-service/status)
* [http://localhost/services/management-service/logs](http://localhost/services/management-service/logs)

`localhost`를 노드 IP 주소로 바꿔도 됩니다.

## 가디언 등록

네트워크에 등록하려면 [가디언 등록](register-your-guardian-on-chain.md)으로 이동합니다(Metamask 필요)

## ORBS 스테이킹하기

1. 가디언 지갑으로 스테이킹할 ORBS 토큰을 전송하세요.
2. 스테이킹에는 약간의 가스비(ETH)가 필요합니다.
3. 스테이킹 지갑앱 [Tetra](https://staking.orbs.network/) 에 접속하세요.
4. "스테이킹" 버튼을 눌러서 스테이킹 절차를 진행하세요.

{% hint style="info" %}
스테이킹된 ORBS 토큰을 다시 언스테이킹하는데는 14일이 소요됩니다.
{% endhint %}

<br>
