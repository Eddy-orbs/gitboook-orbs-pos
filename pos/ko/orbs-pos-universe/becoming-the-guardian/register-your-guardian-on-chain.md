# 온체인에 가디언 등록하기

가디언 목록에 올라가고 위임을 받아 스테이킹 보상을 받으려면 가디언 등록이 필수입니다.

## 사전 준비

* 가디언 지갑(Metamask)
  * 하드웨어 지갑을 쓰는 경우 Metamask에 연결해주세요.
* 노드 지갑 주소
* 본인이 소유한 공인 IP 주소(또는 AWS Elastic IP)
* 본인을 소개하는 웹사이트 주소
* ETH 1개
* POL 1개(폴리곤 네트워크 등록 시 선택)

## 노드 주소로 자금 보내기

노드는 이더리움·폴리곤 네트워크에서 가끔 트랜잭션을 보낼 자금이 필요합니다. 예를 들어 노드가 위원회에 들어갈 준비가 되면 네트워크로 트랜잭션이 전송됩니다. ORBS 노드 주소로 ETH 1개(이더리움 네트워크)와 POL 1개(폴리곤 네트워크)를 보내야 합니다.

{% hint style="info" %}
Orbs 노드 주소에는 PoS 컨트랙트로 보내는 트랜잭션 가스를 충당할 만큼의 ETH가 있어야 합니다. 가디언은 Orbs 노드 주소에 최소 0.5 ETH 이상이 있는지 주기적으로 확인할 책임이 있습니다.
{% endhint %}

## 가디언 등록

네트워크에 등록하려면 [ORBS 가디언 포털](https://guardians.orbs.network/registration)로 이동합니다(Metamask 연결 필요).

1. Chrome 브라우저에서 [가디언 포털](https://guardians.orbs.network/)을 엽니다.
2. Metamask(가디언 지갑)를 연결하고 등록 절차를 진행합니다.
3. 가디언 지갑에 ORBS를 입금한 뒤 [TETRA](https://staking.orbs.network)로 스테이킹합니다.

참고:

* [ ] 이더리움 네트워크의 "노드 지갑"에 ETH 1개 입금 필요
* [ ] 폴리곤 네트워크의 "노드 지갑"에 POL 1개 입금 필요(폴리곤 스테이킹 시 선택)
* [ ] 보상 및 지원 펀드 대상은 선출된 상위 22명 가디언뿐입니다.

<div align="center"><figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption><p>가디언 포털 화면</p></figcaption></figure></div>

## 폴리곤 네트워크 지원

**신규 가디언**은 이더리움과 폴리곤 모두에 등록하는 것을 권장합니다. [가디언 등록](https://guardians.orbs.network/registration) 링크를 사용하세요.

인증(Certified)을 받으려면 이더리움 등록이 필수입니다.

네트워크 전환은 언어 선택 근처 우측 상단에서 할 수 있습니다.

## 가디언이 올바르게 등록되었는지 확인

[네트워크 상태 페이지](https://status.orbs.network/)에서 가디언 이름이 목록에 있고 모든 서비스가 녹색인지 확인합니다(동기화까지 몇 시간 걸릴 수 있습니다).
