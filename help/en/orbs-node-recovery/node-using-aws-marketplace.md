# Node using AWS Marketplace

## Solution 1. Restart your AWS EC2

This solution tries simple server restarting.

* Please get logged in AWS console

### Instructions

1. open [https://aws.amazon.com/](https://aws.amazon.com/) and log in with your guardian's account.
2. Please make sure right region is selected where your node is installed. (Region info is on the right top)
3.  Go to EC2 service menu.

    <figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>
4.  Select Instances(running)

    <figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>
5.  Select checkbox of current instance and try reboot instance.

    <figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>
6. Please wait rebooting and check [status page](https://status.orbs.network/) after 30-60 mins. If your node is still offline, try the next solution.

***

## Solution 2. Re-install Node

[Delete stack](https://orbs-doc.gitbook.io/pos/orbs-pos-universe/becoming-the-guardian/decommissioning-node#node-using-aws-marketplace) and re-install following [the guide doc](https://orbs-doc.gitbook.io/pos/orbs-pos-universe/becoming-the-guardian/setup-node-using-aws-marketplace).

{% hint style="warning" %}
Please note Elastic IP will be changed after re-install. You have to update guardian info at the [guardians portal](https://guardians.orbs.network/). To maintain Elastic IP, please allocate one manually and use it.
{% endhint %}

If it keeps offline,  please contact [eddy@orbs.com](mailto:eddy@orbs.com).



