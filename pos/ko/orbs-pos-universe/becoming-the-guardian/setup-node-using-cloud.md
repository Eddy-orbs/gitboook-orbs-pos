# 클라우드로 노드 구성

## 클라우드 서비스(AWS)로 노드 구성

Amazon Web Services 같은 클라우드로 노드 인프라와 공인 IP를 확보할 수 있습니다. 물리 공간이 없거나 직접 공인 IP를 갖지 않은 경우에 적합합니다.

이 단계별 가이드는 새 노드를 만들어 기존 Orbs 네트워크에 연결하는 과정을 안내합니다.

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

## 사전 준비

* 원격 PC(Ubuntu 또는 macOS)
* AWS 계정
* Elastic IP 주소(AWS)
* 가디언 지갑(스테이킹, 보상) — 하드웨어 지갑 권장
* 노드 운용용 ERC20 주소의 개인키. 자세한 내용은 [아래](setup-node-using-cloud.md#allocate-orbs-node-address-and-private-key)를 참고하세요.
* 이더리움 RPC 엔드포인트 URL([Infura](https://www.infura.io/) 무료 등급 가능)
* Linux/Unix 터미널 사용에 익숙할 것

## 도구 설치

* [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
* [Node.js 18 이상](https://nodejs.org/en/download)
* [Terraform](https://developer.hashicorp.com/terraform/install)
  * Terraform 설치 후 버전 v0.12.23을 선택합니다.
    * v0.12.23 설치

        ```
        tfenv install 0.12.23
        ```
    * v0.12.23 선택

        ```
        tfenv use 0.12.23
        ```
    * v0.12.23이 선택되었는지 확인

        ```
        terraform -v
        ```

## SSH 공개키·비밀키 생성

Orbs 노드 인스턴스에 쓸 SSH 공개키가 이미 있다면 이 단계를 건너뛸 수 있습니다.

배포 스크립트 실행과 EC2 리소스 설정을 위해 유효한 공개키/비밀키 쌍이 필요합니다. 키 파일은 비밀로 유지하고, 설정 시 `orbs-node.json`에 공개키 파일 경로만 넣는 식으로 전달합니다(아래 설명).

생성한 키에는 **패스프레이즈를 두지 마세요**. [GitHub](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) 튜토리얼 등 어떤 방법으로든 생성해도 됩니다.

요약하면 다음을 실행합니다.

```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

* `-C "[your\_email@example.com](mailto:your_email@example.com)"`는 선택 주석입니다.

## AWS 자격 증명 설정

AWS 콘솔에서 액세스 키를 만들고 키 ID와 시크릿 액세스 키를 저장합니다.

`aws configure`를 실행하고 저장한 키 ID, 시크릿 키, 선택한 리전을 입력합니다.

<div align="left"><figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption><p>AWS 설정 예시(기본 default 프로필)</p></figcaption></figure></div>

## Amazon에서 고정 IP 할당

프로비저닝하는 Orbs 노드는 네트워크와 통신하려면 **고정 IP(Elastic IP)**가 있어야 합니다.

* AWS 콘솔에 로그인합니다.
* 노드를 배포할 리전을 고릅니다(예: `ca-central-1`).
* "EC2" > "Elastic IPs"로 이동합니다.
* "Allocate Elastic IP address"를 누르고 안내에 따라 Elastic IP를 1개 할당합니다.

AWS CLI로도 할 수 있습니다: `aws ec2 allocate-address --region 원하는-리전`.

노드 IP와 리전은 나중에 노드 설정 파일에 사용합니다. IP는 [가디언 등록](register-your-guardian-on-chain.md) 단계에서도 필요합니다.

## Orbs 노드 주소와 개인키 할당

Orbs 노드 주소는 표준 이더리움 주소입니다. 이 주소는 (1) Orbs 네트워크에서 블록 서명, (2) 이더리움의 Orbs PoS 스마트 컨트랙트로 트랜잭션 전송에 사용됩니다. 따라서 Orbs 노드는 해당 주소의 개인키를 저장·사용하며, **가디언 개인키와는 달라야 합니다.**

정상 동작 시 Orbs 노드는 이더리움의 PoS 컨트랙트로 자동으로 트랜잭션을 보냅니다(보상 분배 실행, 네트워크 동기화 및 위원회 진입 준비 신호 등). Orbs 노드 주소에는 이런 트랜잭션 가스를 충당할 만큼의 ETH가 있어야 하며, 가디언은 Orbs 노드 주소에 **최소 0.5 ETH** 이상이 있는지 주기적으로 확인할 책임이 있습니다.

참고 — 등록을 완료하려면 Orbs 노드 주소에 **최소 1 ETH**로 시작해야 합니다.

Orbs 노드 주소와 개인키는 안전하게 생성해야 하며, 개인키는 노드 배포 시 필요합니다(아래 참고).

Orbs 노드 주소는 [가디언 등록](register-your-guardian-on-chain.md) 단계에서도 필요합니다.

Orbs 주소는 등록을 갱신해 변경할 수 있습니다. 노드 주소를 바꾸기 전에는 노드를 내리고 다시 배포해야 합니다.

Orbs 노드 개인키는 안전하게 보관하세요.

필요하면 [wallet generator](https://iancoleman.io/bip39/)로 새 지갑과 개인키를 만들 수 있습니다.

## NPM으로 Polygon 설치

{% hint style="info" %}
여기서의 "Polygon"은 Orbs 네트워크 설치를 자동화하는 소프트웨어 이름입니다.
{% endhint %}

설치:

```
npm install -g @orbs-network/polygon
```

이미 Polygon을 설치했고 새로 배포하는 경우 `npm update -g @orbs-network/polygon`으로 업데이트하는 것을 권장합니다.

## 전용 배포 폴더 만들기

배포 전용 폴더를 만듭니다. 예: `mkdir orbs-node`. 이 폴더에는 설정 파일, 로그, 이후 AWS 리소스를 제거할 때 필요한 산출물이 들어갑니다.

**안내를 모두 마친 뒤 이 폴더를 백업하고 삭제하지 마세요.**

작업 디렉터리를 배포 폴더로 `cd orbs-node` 한 뒤 아래를 진행합니다.

## JSON 설정 파일

배포용 설정 파일 `orbs-node.json`을 만듭니다.

`orbs-node.json` 내용은 다음과 같아야 합니다.

```
{
    "name": "<orbs node name>",
    "awsProfile": "<aws profile>",
    "sshPublicKey": "<ssh access public key file>",
    "orbsAddress": "<orbs node ethereum address>",
    "region": "<aws region>",
    "publicIp": "<node ip>",
    "nodeSize": "r5.large",
    "nodeCount": 0,
    "cachePath": "./_terraform",
    "incomingSshCidrBlocks": ["<ssh source cird block>",...],
    "boyarAutoUpdate": true,
    "managementConfig": {
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
                    "EthereumEndpoint": "<ethereum endpoint url>"
                }
            }
        }
    }
}
```

각 항목:

* `<orbs node name>` — 일부 AWS 리소스 이름의 접두·접미로 쓰여 노드 리소스를 구분합니다. S3 버킷 이름 규칙을 만족해야 합니다(예: `_` 사용 불가 등).
* `<aws profile>` — "default"를 쓰거나, 여러 AWS 계정을 프로필로 쓰는 경우 해당 계정에 맞게 미리 설정된 프로필 이름을 씁니다.
* `<ssh access public key file>` — SSH 공개키 파일 경로(생략 시 기본 `~/.ssh/id_rsa.pub`).
* `<orbs node ethereum address>` — Orbs 노드 이더리움 주소, EIP-55 체크섬 대문자·소문자 형식이되 **선행 `0x` 없이** 입력합니다.
* `<aws region>` — AWS 리전 이름. 새 노드는 이 리전에 프로비저닝됩니다. 예: `ca-central-1`. 앞서 만든 Elastic IP와 동일한 리전이어야 합니다.
* `<node ip>` — Orbs 노드에 연결될 고정 IP. 선택한 리전에서 할당·미연결 상태인 Elastic IP여야 합니다.
* `<ethereum endpoint url>` — 동기화된 이더리움 노드 URL(예: [Infura](https://infura.io) `https://mainnet.infura.io/v3/<키>` 형식).&#x20;
* `<ssh source cird block>` — 노드로 SSH 접속을 허용할 CIDR 블록 하나 이상. 공개키 없이는 접속할 수 없으며, 트러블슈팅 시에만 필요합니다. 표준 CIDR 형식입니다. 범위 밖 IP는 SSH 키가 있어도 접속할 수 없습니다. 모든 IP에서 SSH를 허용하려면 `"incomingSshCidrBlocks": ["0.0.0.0/0"],`를 사용합니다.

## Polygon CLI로 노드 배포

Orbs 노드 개인키를 **선행 `0x` 없이** 환경 변수에 잠시 넣습니다. 실수로 소스에 커밋되는 것을 막기 위함입니다.

개인키는 64자 16진 문자열입니다. 예: `f5f83Ee70a85fFF2exxxxxxxxxxxxxxxxxxxxxxxxxxx334932F34C8D629165Ed`.

예시:

```
export ORBS_NODE_PRIVATE_KEY=f5f83Ee70a85fFF2exxxxxxxxxxxxxxxxxxxxxxxxxxx334932F34C8D629165Ed
```

배포 실행:

```
polygon create -f orbs-node.json --orbs-private-key $(echo $ORBS_NODE_PRIVATE_KEY)
```

노드 생성 중 Terraform 캐시가 `cachePath`에 지정한 폴더에 생깁니다. 나중에 정리할 때를 위해 백업해 두세요.

필요 시 프로비저닝한 리소스를 모두 제거하는 명령:

```
polygon destroy -f orbs-node.json
```

#### 중요!

배포 후 다음을 반드시 백업하고 안전하게 보관하세요.

* **Orbs 키**(`<orbs private key file>` 및 SSH 키 등 설정에 사용한 자격 증명)
* `_terraform` 캐시 폴더 — 노드 제거 또는 재배포에 필요
* `orbs-node.json` 파일

`<orbs private key file>` 같은 민감 정보를 보안이 약한 위치에 두지 마세요.

## Orbs 노드 설정 변경

**설정 파일을 바꿀 때는 반드시 노드를 중지한 상태에서만 하세요.**

갱신 절차:

1. `polygon destroy` 실행(예: `polygon destroy -f orbs-node.json`)
2. 필요에 따라 JSON 설정을 수정
3. [polygon create](setup-node-using-cloud.md#deploy-the-node-using-polygon-cli) 실행

## 노드가 올바르게 배포되었는지 확인

모든 서비스가 오류 없이 시작되었는지 확인합니다.

```
http://<node ip>/services/boyar/status
```

PoS 네트워크에서 노드 상태를 확인합니다(지표는 최종 확정 때문에 약 10분 지연될 수 있음).

```
http://<node ip>/services/management-service/status
```

## 가디언 등록

네트워크에 등록하려면 [가디언 등록](register-your-guardian-on-chain.md)으로 이동합니다(Metamask 필요).

## 문제 해결

1. Terraform에서 IP가 없다는 오류가 나면 `orbs-node.json`의 IP와 리전 조합이 맞는지 확인합니다.
2. 메트릭 페이지가 응답하지 않으면 이더리움 노드 동기화가 아직 끝나지 않았을 수 있습니다. 몇 시간 걸릴 수 있습니다.
3. Terraform 오류가 나면 시스템 시각이 올바른지 확인합니다.
4. 그 외 문제는 Orbs 코어 팀에 문의하세요.

<br>
