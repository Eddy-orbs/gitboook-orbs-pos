---
description: ⚠️ 베타버전으로 2개의 지역만 현재 지원합니다. 오브스 노드는 설치 지역을 제한하지 않습니다.
---

# 노드 설치 방법 (AWS 스택)

AWS 에서 지원하는 이미지(AMI)를 통해 노드를 설치합니다.

Amazon Web Services 같은 클라우드로 노드 인프라와 공인 IP를 확보할 수 있습니다. 터미널 환경에 익숙하지 않거나, 별도의 서버가 없고 직접 공인 IP를 확보하기 어려운 경우에 적합합니다.

이 단계별 가이드는 새 노드를 만들어 Orbs 네트워크에 연결하는 과정을 안내합니다.

## 사전 준비

* 웹 브라우저 (크롬브라우저 권장)
* AWS 계정
* AWS Elastic IP (Optional)
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

## AWS 스택 설치

1. 크롬 브라우저에서  [AWS 콘솔 웹사이트](https://console.aws.amazon.com/) 에 접속하여 로그인 &#x20;
2. &#x20;아래 LAUNCH STACK 버튼을 눌러서 설치화면을 열어주세요:\
   [![Launch Stack us-east-1](https://camo.githubusercontent.com/45efe5b594d50802654c8f5ff0ab65e8e9c6ab7367f4d7cb4ac737a4ed4fdc75/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f4c61756e6368253230537461636b2d75732d2d656173742d2d312d6f72616e67653f7374796c653d666f722d7468652d6261646765266c6f676f3d616d617a6f6e617773)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=orbs-boyar-validator\&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct-autonet-github-us-east-1.yaml)

## 빠른 스택 생성

준비된 정보를 입력하세요

1. 필수 입력 정보
   1. **Stack name(스택 이름)**: 원하는 이름을 넣으세요 (ie. `my-orbs-node-name`)
   2. **Ethereum RPC endpoint** (ie. `https://mainnet.infura.io/v3/06b6...b2d7e`)
   3. **Orbs node address without 0x** (ie. `246abE5D...000`)
   4. **Orbs private key without 0x** (ie. `0ab6F...000`)
   5. **SSH access CIDR**: `0.0.0.0/0` 또는 원하는 값(의미를 아는 경우)
   6. **Node/status access CIDR**: `0.0.0.0/0` 또는 원하는 값(의미를 아는 경우)
2. 선택 입력 정보
   1. EIP (탄력적 IP) (선택 옵션): 이미 할당받은 ip 주소가 있다면 여기 입력하세요. 할당받은 IP주소가 없다면 비워두세요. 설치중에 자동으로 IP주소를 할당받아서 설치 후에 주소값을 알려줍니다.
   2. Optional EC2 Key Pair name(선택 옵션): SSH접속을 위한 키값
3. `Create stack(스택생성)` 버튼을 클릭하세요.
4.  설치 상태가 `CREATE_COMPLETE` 완료가 될때까지 기다려주세요. (약 10분 후 새로고침을 클릭결과해보세요)<br>

    <div align="left" data-with-frame="true"><figure><img src="../../.gitbook/assets/image (11).png" alt="" width="346"><figcaption></figcaption></figure></div>

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
