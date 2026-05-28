# AWS 마켓으로 설치한 경우

## 방법1. AWS EC2 재시작

이 방법은 AWS의 EC2 서버를 단순히 재부팅 시도하는 방법입니다.&#x20;

* 준비물: AWS console 로그인

### 방법 순서

1. [https://aws.amazon.com/](https://aws.amazon.com/) 에 접속하여 가디언의 AWS 계정으로 로그인합니다.
2. 처음 노드를 설치한 AWS Region이 선택되어 있는지 확인. (지역 선택은 우측 상단에 있습니다)
3.  EC2 서비스 메뉴로 이동하세요.

    <figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>
4.  Instance(인스턴스) 목록을 선택하세요.

    <figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>
5.  노드의 Instance(인스턴스)를 앞쪽 체크박스를 선택하고, "인스턴스 상태", "인스턴스 재부팅"을 순서대로 클릭해서 EC2 서버를 재시작(재부팅) 시킵니다.<br>

    <figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>
6. 약 30\~60분 후,  [https://status.orbs.network/](https://status.orbs.network/) 에서 본인의 서버가 offline이 아닌, 정상적인 체크박스로 나타나는지 확인해보세요.
7. 만일 계속 offline이라면 아래 방법2를 수행해보세요.

***

## Solution 2. Re-install Node <a href="#solution-2.-re-install-node" id="solution-2.-re-install-node"></a>

[스택을 제거](https://orbs-doc.gitbook.io/pos/orbs-pos-universe-ko/orbs-pos/becoming-the-guardian/decommissioning-node#node-using-aws-marketplace)하고 가이드 문서에 따라 [다시 설치](https://orbs-doc.gitbook.io/pos/orbs-pos-universe-ko/orbs-pos/becoming-the-guardian/aws)하세요.

{% hint style="warning" %}
마켓에서 설치하면서 자동으로 Elastic IP를 할당받았던 경우, 재설치하면 IP주소가 변경됩니다. 이 경우, 반드시 가디언 포털에서 가디언 IP정보를 바뀐 주소로 업데이트 수정해주어야 합니다. 수정하지 않기 위해서는 수동으로 Elastic IP를 할당받아서 마켓설치시에 수동입력을 해주세요.
{% endhint %}

만일 계속 문제가 해결되지 않는다면 , eddy@orbs.com 으로 도움을 요청하세요.

