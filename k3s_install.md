## k3s部署
1）选择合适版本：
[https://update.k3s.io/v1-release/channels](https://update.k3s.io/v1-release/channels)
2）利用官方命令安装

- 在线安装
```shell
#关闭防火墙
systemctl stop firewalld
systemctl disable firewalld.service
#关闭SeLinux
setenforce 0
sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config
#关闭 swap
swapoff -a    临时关闭
vim /etc/fstab  注释掉swap的行，永久关闭
free 查看swap是否关闭


#安装1.19版本，使用docker，并禁用traefik自动安装
curl -sfL https://get.k3s.io  | INSTALL_K3S_MIRROR=cn INSTALL_K3S_VERSION=v1.19.16+k3s1 INSTALL_K3S_EXEC="--docker --disable traefik" sh -

[INFO]  Using v1.19.16+k3s1 as release
[INFO]  Downloading hash https://github.com/k3s-io/k3s/releases/download/v1.19.16+k3s1/sha256sum-amd64.txt
[INFO]  Downloading binary https://github.com/k3s-io/k3s/releases/download/v1.19.16+k3s1/k3s
[INFO]  Verifying binary download
[INFO]  Installing k3s to /usr/local/bin/k3s
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
软件包 yum-utils-1.1.31-54.el7_8.noarch 已安装并且是最新版本
无须任何处理
已加载插件：fastestmirror
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
rancher-k3s-common-stable                                                                     | 2.9 kB  00:00:00
rancher-k3s-common-stable/primary_db                                                          | 3.4 kB  00:00:02
正在解决依赖关系
--> 正在检查事务
---> 软件包 k3s-selinux.noarch.0.1.1-1.el7 将被 安装
--> 解决依赖关系完成

依赖关系解决

=====================================================================================================================
 Package                  架构                版本                      源                                      大小
=====================================================================================================================
正在安装:
 k3s-selinux              noarch              1.1-1.el7                 rancher-k3s-common-stable               16 k

事务概要
=====================================================================================================================
安装  1 软件包

总下载量：16 k
安装大小：94 k
Downloading packages:
警告：/var/cache/yum/x86_64/7/rancher-k3s-common-stable/packages/k3s-selinux-1.1-1.el7.noarch.rpm: 头V4 RSA/SHA1 Signature, 密钥 ID e257814a: NOKEY
k3s-selinux-1.1-1.el7.noarch.rpm 的公钥尚未安装
k3s-selinux-1.1-1.el7.noarch.rpm                                                              |  16 kB  00:00:03
从 https://rpm.rancher.io/public.key 检索密钥
导入 GPG key 0xE257814A:
 用户ID     : "Rancher (CI) <ci@rancher.com>"
 指纹       : c8cf f216 4551 26e9 b9c9 18be 925e a29a e257 814a
 来自       : https://rpm.rancher.io/public.key
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  正在安装    : k3s-selinux-1.1-1.el7.noarch                                                                     1/1
  验证中      : k3s-selinux-1.1-1.el7.noarch                                                                     1/1

已安装:
  k3s-selinux.noarch 0:1.1-1.el7

完毕！
[INFO]  Creating /usr/local/bin/kubectl symlink to k3s
[INFO]  Creating /usr/local/bin/crictl symlink to k3s
[INFO]  Skipping /usr/local/bin/ctr symlink to k3s, command exists in PATH at /usr/bin/ctr
[INFO]  Creating killall script /usr/local/bin/k3s-killall.sh
[INFO]  Creating uninstall script /usr/local/bin/k3s-uninstall.sh
[INFO]  env: Creating environment file /etc/systemd/system/k3s.service.env
[INFO]  systemd: Creating service file /etc/systemd/system/k3s.service
[INFO]  systemd: Enabling k3s unit
Created symlink from /etc/systemd/system/multi-user.target.wants/k3s.service to /etc/systemd/system/k3s.service.
[INFO]  systemd: Starting k3s
```
```shell
(py39) [root@edgenode1 home]# kubectl get node
NAME        STATUS   ROLES    AGE     VERSION
edgenode1   Ready    master   3m34s   v1.19.16+k3s1
```
离线安装：
[https://rancher.com/docs/k3s/latest/en/installation/airgap/](https://rancher.com/docs/k3s/latest/en/installation/airgap/)
1.在[releases](https://github.com/rancher/k3s/releases)下载k3s及k3s-airgap-images-amd64.tar。
2.将k3s文件移动到**/usr/local/bin目录下，并chmod +x /usr/local/bin/k3s**
**3.将**k3s-airgap-images-amd64.tar移动到**/var/lib/rancher/k3s/agent/images/**
**4.**wget [https://get.k3s.io](https://get.k3s.io) > install.sh,chmod +x install.sh
5.执行INSTALL_K3S_SKIP_DOWNLOAD=true INSTALL_K3S_EXEC="--docker --disable traefik"  ./install.sh
设置kubeconfig
```shell
vi /etc/profile
#添加
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml

source /etc/profile
systemctl enable k3s
```
外部访问集群:
将该文件copy到对应机器~/.kube/config即可。
```shell
#可以查看密钥
kubectl -n kube-system edit secrets/k3s-serving

apiVersion: v1
data:
  tls.crt: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUI4ekNDQVptZ0F3SUJBZ0lJTWhZTEs4MS9ZbDh3Q2dZSUtvWkl6ajBFQXdJd0l6RWhNQjhHQTFVRUF3d1kKYXpOekxYTmxjblpsY2kxallVQXhOalUyTXpJeE16RTVNQjRYRFRJeU1EWXlOekE1TVRVeE9Wb1hEVEl6TURZeQpPVEEyTkRZMU0xb3dIREVNTUFvR0ExVUVDaE1EYXpOek1Rd3dDZ1lEVlFRREV3TnJNM013V1RBVEJnY3Foa2pPClBRSUJCZ2dxaGtqT1BRTUJCd05DQUFUL3d4elRkTTRuck03cis1MHYvNEhlTVF3WGNzbzQ4VnM3dmZGSEVtYSsKQms3YnRWUWFZcmE4dEdiWDJwTVowTGhvaVRMWHB5MXI4aUw5R2pHalpUNzJvNEc5TUlHNk1BNEdBMVVkRHdFQgovd1FFQXdJRm9EQVRCZ05WSFNVRUREQUtCZ2dyQmdFRkJRY0RBVEFmQmdOVkhTTUVHREFXZ0JSTzY0cjIzUVFZCmY4K3h1K2laRld2bVRxYUo5REJ5QmdOVkhSRUVhekJwZ2dwcmRXSmxjbTVsZEdWemdoSnJkV0psY201bGRHVnoKTG1SbFptRjFiSFNDSkd0MVltVnlibVYwWlhNdVpHVm1ZWFZzZEM1emRtTXVZMngxYzNSbGNpNXNiMk5oYklJSgpiRzlqWVd4b2IzTjBod1FLQUFJSmh3UUtaSFNzaHdRS0t3QUJod1IvQUFBQk1Bb0dDQ3FHU000OUJBTUNBMGdBCk1FVUNJUUR1YlVLVnBkdUhzRjBHVnFiNTl6enJtQmhUak1sQ0xQV2ZNYml2U0N4NDFBSWdOZHR5UVZYaDNUclQKeFd3WGk1WHU0b3A4d3hwMHMvbFVtY2lnV1lTTXJkcz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
  tls.key: LS0tLS1CRUdJTiBFQyBQUklWQVRFIEtFWS0tLS0tCk1IY0NBUUVFSUFPYkFjQThrcDJyNll5TjlKUkJHSXZ4SmhnZ1JlKzBZTFNrSXYrL1hYdE1vQW9HQ0NxR1NNNDkKQXdFSG9VUURRZ0FFLzhNYzAzVE9KNnpPNi91ZEwvK0IzakVNRjNMS09QRmJPNzN4UnhKbXZnWk8yN1ZVR21LMgp2TFJtMTlxVEdkQzRhSWt5MTZjdGEvSWkvUm94bzJVKzlnPT0KLS0tLS1FTkQgRUMgUFJJVkFURSBLRVktLS0tLQo=
kind: Secret
metadata:
  annotations:
    listener.cattle.io/cn-10.0.2.9: 10.0.2.9
    listener.cattle.io/cn-10.43.0.1: 10.43.0.1
    listener.cattle.io/cn-10.100.116.172: 10.100.116.172
    listener.cattle.io/cn-127.0.0.1: 127.0.0.1
    ....
```
## 

