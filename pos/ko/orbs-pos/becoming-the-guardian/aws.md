---
description: ⚠️ 베타버전으로 1개의 지역만 현재 지원합니다. 참고로 오브스 노드는 설치 지역을 제한하지 않습니다.
---

# 노드 설치 방법 (AWS 마켓플레이스)

AWS 에서 지원하는 이미지(AMI)를 통해 노드를 설치합니다.

Amazon Web Services 같은 클라우드로 노드 인프라와 공인 IP를 확보할 수 있습니다. 터미널 환경에 익숙하지 않거나, 별도의 서버가 없고 직접 공인 IP를 확보하기 어려운 경우에 적합합니다.

이 단계별 가이드는 새 노드를 만들어 Orbs 네트워크에 연결하는 과정을 안내합니다.

## 사전 준비

* 웹 브라우저 (크롬브라우저 권장)
* AWS 계정
* AWS Elastic IP (선ㄹㅇㄴ)
* 가디언 지갑(스테이킹 및 보상수령) — 하드웨어 지갑 권장
* 노드 운용용 ERC20 주소의 개인키. 자세한 내용은 [아래](aws.md#orbs)를 참고하세요.
* 이더리움 RPC 엔드포인트 URL([Infura](https://www.infura.io/) 무료 등급으로 가능)

## Orbs 노드 주소와 개인키 설정

Orbs 노드 주소는 표준 이더리움 주소입니다. 이 주소는 (1) Orbs 네트워크에서 블록 서명, (2) 이더리움의 Orbs PoS 스마트 컨트랙트로 트랜잭션 전송에 사용됩니다. 따라서 Orbs 노드는 해당 주소의 개인키를 저장·사용하며, **가디언 스테이킹 주소의 개인키와는 달라야 합니다.**

정상 동작 시 Orbs 노드는 이더리움의 PoS 컨트랙트로 자동으로 트랜잭션을 보냅니다(보상 분배 실행, 네트워크 동기화 및 위원회 진입 준비 신호 등). Orbs 노드 주소에는 이런 트랜잭션 가스를 충당할 만큼의 ETH가 있어야 하며, 가디언은 Orbs 노드 주소에 **최소 0.5 ETH** 이상이 있는지 주기적으로 확인할 책임이 있습니다.

참고 — 등록을 완료하려면 Orbs 노드 주소에 **최소 1 ETH**로 시작해야 합니다.

Orbs 노드 주소와 개인키는 안전하게 생성, 보관해야 하며, 개인키는 노드 설치 시 필요합니다(아래 참고).

Orbs 노드 주소는 [가디언 등록](https://orbs-doc.gitbook.io/pos/orbs-pos-universe-ko/orbs-pos/becoming-the-guardian/register-your-guardian-on-chain) 단계에서도 필요합니다.

Orbs 주소는 등록을 갱신해 변경할 수 있습니다. 노드 주소를 바꾸기 전에는 노드를 내리고 다시 설치해야 합니다.

_**Orbs 노드 개인키는 반드시 안전한 곳 보관하세요.**_

필요하면 [wallet generator](https://iancoleman.io/bip39/)로 새 지갑과 개인키를 만들 수 있습니다.

### AWS 마켓플레이스에서 Orbs Node 앱 구매 (무료) <a href="#subscribe-orbs-node-app-from-aws-marketplace" id="subscribe-orbs-node-app-from-aws-marketplace"></a>

1. 브라우저에서 [AWS 마켓플레이스](https://aws.amazon.com/marketplace)에 접속해서 aws 계정으로 로그인하세요.
2. [Orbs Network Full Node](https://aws.amazon.com/marketplace/pp?sku=51c282skmep4qmamtmspsi8f3) 애플리케이션으로 접속하세요.
3.  `View purchase option(구매 옵션 보기)` 를 클릭하세요.



    <div align="left" data-with-frame="true"><figure><img src="https://orbs-doc.gitbook.io/pos/~gitbook/image?url=https%3A%2F%2F3545397290-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252Fg5oTTPwJkXaserapTxr7%252Fuploads%252FP1ay9DhcpCc5oru9wXTz%252Fimage.png%3Falt%3Dmedia%26token%3Dfd09be62-5aff-4f8c-b3c6-4d641cb28c29&#x26;width=768&#x26;dpr=3&#x26;quality=100&#x26;sign=405529d8&#x26;sv=2" alt=""><figcaption></figcaption></figure></div>
4. 스크롤을 내리고 `Subscribe(구독하기)` 를 클릭하세요. 몇 분이 소요될 수 있습니다.
5.  `Launch your software(소프트웨어 시작)`을 클릭하세요.

    You can see Launch button after subscription process done

## Orbs Network Full Node 설치하기 <a href="#launch-orbs-network-full-node" id="launch-orbs-network-full-node"></a>

NOTE: 현재 베타기간으로 1개의 지역만 설치지원합니다. 오브스 노드 설치는 꼭 자신의 거주지역이 아니어도 상관없습니다.

1.  **리전 선택하기**



    <div align="left" data-with-frame="true"><figure><img src="https://orbs-doc.gitbook.io/pos/~gitbook/image?url=https%3A%2F%2F3545397290-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252Fg5oTTPwJkXaserapTxr7%252Fuploads%252Fz6W76sFpWiFgTdZfyUjF%252Fimage.png%3Falt%3Dmedia%26token%3Dcf5f56db-cc22-45d2-a1d0-a13102afd44d&#x26;width=768&#x26;dpr=3&#x26;quality=100&#x26;sign=4bfc9428&#x26;sv=2" alt=""><figcaption></figcaption></figure></div>
2.  **시작하기:** `Launch with CloudFormation (CloudFormation으로 시작)` 버튼 클릭



    <div align="left" data-with-frame="true"><figure><img src="https://orbs-doc.gitbook.io/pos/~gitbook/image?url=https%3A%2F%2F3545397290-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252Fg5oTTPwJkXaserapTxr7%252Fuploads%252FaRD8LVmIC4ggexxAmmIq%252Fimage.png%3Falt%3Dmedia%26token%3D10cbd8e7-3fb9-41be-9862-3e07b792f03c&#x26;width=768&#x26;dpr=3&#x26;quality=100&#x26;sign=b228540d&#x26;sv=2" alt=""><figcaption></figcaption></figure></div>
3.  **스택 생성:** `Next(다음)` 버튼을 클릭하세요. 만약 S3 URL입력칸이 비어있다면 아래 주소를 붙여넣으세요: Amazon S3 URL: `https://awsmp-cft-053155443450-1579814207723.s3.us-east-1.amazonaws.com/419ae877-2d01-4c05-84c8-d20e52986770/419ae877-2d01-4c05-84c8-d20e52986770/template-medium-ami-direct-autonet.yaml`

    <div align="left" data-with-frame="true"><figure><img src="https://orbs-doc.gitbook.io/pos/~gitbook/image?url=https%3A%2F%2F3545397290-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252Fg5oTTPwJkXaserapTxr7%252Fuploads%252F51dkM17VaOqCmr6FdcVJ%252Fimage.png%3Falt%3Dmedia%26token%3Da7453afe-1a6b-4be2-9a6e-64d44bee8210&#x26;width=768&#x26;dpr=3&#x26;quality=100&#x26;sign=77d45704&#x26;sv=2" alt=""><figcaption></figcaption></figure></div>
4. **스택 세부 정보 지정**: 준비된 정보를 입력하세요
   1. 필수 입력 정보
      1. **Stack name(스택 이름)**: 원하는 이름을 넣으세요 (ie. `my-orbs-node-name`)
      2. **Ethereum RPC endpoint** (ie. `https://mainnet.infura.io/v3/06b6...b2d7e`)
      3. **Orbs node address without 0x** (ie. `246abE5D...000`)
      4. **Orbs private key without 0x** (ie. `0ab6F...000`)
      5. **SSH access CIDR**: `0.0.0.0/0` 또는 원하는 값(의미를 아는 경우)
      6. **Node/status access CIDR**: `0.0.0.0/0` 또는 원하는 값(의미를 아는 경우)
   2. 선택 입력 정보
      1. EIP (탄력적 IP) (선택 옵션): 이미 할당받은 ip 주소가 있다면 여기 입력하세요. 할당받은 IP주소가 없다면 비워두세요. 설치중에 자동으로 IP주소를 할당받아서 설치 후에 주소값을 알려줍니다. _EIP는 따로 할당받아서 사용하는 것을 권장합니다. 자동할당을 사용할 경우 재설치시 IP가 변경되어 번거로울 수 있습니다._
      2. Optional EC2 Key Pair name(선택 옵션): SSH접속을 위한 키값
   3. `Next(다음)` 버튼을 클릭하세요
5. **스택 옵션 구성:** 따로 입력할 값이 없습니다. 바로 `Next(다음)` 버튼을 누르세요.
6. **검토 및 작성:** 스크롤을 내리고 `Submit(전송)` 버튼을 누르세요.
7.  설치 상태가 `CREATE_COMPLETE` 완료가 될때까지 기다려주세요. (약 10분 후 새로고침을 클릭결과해보세요)



    <div align="left" data-with-frame="true"><figure><img src="https://orbs-doc.gitbook.io/pos/~gitbook/image?url=https%3A%2F%2F3545397290-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252Fg5oTTPwJkXaserapTxr7%252Fuploads%252FyKOOAmg6JzR3UCgKtI5P%252Fimage.png%3Falt%3Dmedia%26token%3D3eb78c65-b6dd-476c-8450-61114af76fce&#x26;width=768&#x26;dpr=3&#x26;quality=100&#x26;sign=35771510&#x26;sv=2" alt=""><figcaption></figcaption></figure></div>

## 결과값 저장하기 <a href="#backup-outputs" id="backup-outputs"></a>

<div align="left" data-with-frame="true"><img src="https://orbs-doc.gitbook.io/pos/~gitbook/image?url=https%3A%2F%2F3545397290-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252Fg5oTTPwJkXaserapTxr7%252Fuploads%252FJ5PEHWRUHg5EeLRSSQ3B%252Fimage.png%3Falt%3Dmedia%26token%3D4ce2809b-9fc8-490f-bc13-b8e91ff6e952&#x26;width=768&#x26;dpr=3&#x26;quality=100&#x26;sign=53aad222&#x26;sv=2" alt=""></div>

설치된 스택의 Outputs 탭으로 가서 노드 정보를 따로 저장해두세요:

* **Elastic IP:** 할당받은 IP 주소 (ie. `44.207.238.247`)
* **BoyarStatusUrl:** http://\<your\_ip\_address>/services/boyar/status

### 노드가 올바르게 배포되었는지 확인 <a href="#undefined-4" id="undefined-4"></a>

모든 서비스가 오류 없이 시작되었는지 확인합니다.

```
http://<node ip>/services/boyar/status
```

PoS 네트워크에서 노드 상태를 확인합니다(지표는 최종 확정 때문에 약 10분 지연될 수 있음).

```
http://<node ip>/services/management-service/status
```

## 가디언 등록 <a href="#undefined-5" id="undefined-5"></a>

네트워크에 등록하려면 [가디언 등록](https://orbs-doc.gitbook.io/pos/orbs-pos-universe-ko/orbs-pos/becoming-the-guardian/register-your-guardian-on-chain)으로 이동합니다(Metamask 필요).

## ORBS 스테이킹하기 <a href="#stake-orbs" id="stake-orbs"></a>

1. 가디언 지갑으로 스테이킹할 ORBS 토큰을 전송하세요.
2. 스테이킹에는 약간의 가스비(ETH)가 필요합니다.
3. 스테이킹 지갑앱 [Tetra](https://staking.orbs.network/) 에 접속하세요.
4. "스테이킹" 버튼을 눌러서 스테이킹 절차를 진행하세요.

{% hint style="info" %}
스테이킹된 ORBS 토큰을 다시 언스테이킹하는데는 14일이 소요됩니다.
{% endhint %}
