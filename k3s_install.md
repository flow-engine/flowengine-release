# Build the K3S For the local development environment
# Dependency
English ｜ [中文](/k3s_install-cn.md)

Use virtualbox 6.1 and centOS
* centos 7 CentOS-7-x86_64-Minimal-2009.iso
* Download link：[http://la.mirrors.clouvider.net/CentOS/7.9.2009/isos/x86_64//CentOS-7-x86_64-Minimal-2009.iso](http://la.mirrors.clouvider.net/CentOS/7.9.2009/isos/x86_64//CentOS-7-x86_64-Minimal-2009.iso) You can find more mirror addresses here http://isoredirect.centos.org/centos/7/isos/x86_64/


Dependency：
* docker v20.10.17
* k3s 
* git

# Install the docker

## 0.Setup virtualbox network
Create new nat network,attach all network to NAT

![image.png](https://cdn.nlark.com/yuque/0/2023/gif/35806411/1682310345384-3923ddca-a6b2-4519-b76f-71be28407a4a.gif)

And you can also set the port forwarding to this network
## 1.Install centos and select the image 
  1) select the centos image
  
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35806411/1682310410637-bcd85389-9dce-4520-8d7e-ba9d70927d47.png)
 
  2）memory should higher than 2048M
  
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35806411/1682310829514-ad9723cc-75a9-4e25-a0eb-1d228d62d277.png)

  3) Disk size should higher than 64G
  
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35806411/1682310829514-ad9723cc-75a9-4e25-a0eb-1d228d62d277.png)



4）After create the virtual machine，select the centos image in strage option

![image.png](https://cdn.nlark.com/yuque/0/2023/png/35806411/1682311009054-b7a39c8d-20a6-4e1c-9b99-39c091beae40.png)

5）Start the virtual machine and install the centos

![image.png](https://cdn.nlark.com/yuque/0/2023/png/35806411/1682315347189-2a33ab64-c78f-4b87-b58b-a5cea385f43b.png)

Select "Test this media & install CentOS 7"。

![image.png](https://cdn.nlark.com/yuque/0/2023/png/35806411/1682316382705-d399e0f1-2499-41b5-b976-8adfc00c25a4.png)

![image.png](https://cdn.nlark.com/yuque/0/2023/png/35806411/1682316496899-2f6248a8-e17a-4ed6-b728-784447fd2b5e.png)

select date and time：

![image.png](https://cdn.nlark.com/yuque/0/2023/png/35806411/1682316547731-a4884ff9-e60a-4d8d-8d55-d3c8c8b7b1e9.png)


begin installation and set the password

![image.png](https://cdn.nlark.com/yuque/0/2023/png/35806411/1682318647925-01f8aeb5-b213-448c-9706-4bcc2b0d9951.png?x-oss-process=image%2Fresize%2Cw_750%2Climit_0)

![image.png](https://cdn.nlark.com/yuque/0/2023/png/35806411/1682319737150-d692f8e6-8e51-405a-a95e-ce64c2addbc2.png?x-oss-process=image%2Fresize%2Cw_750%2Climit_0)

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656311341152-c09dbd10-54fd-4cc0-b748-96b90268a701.png?x-oss-process=image%2Fresize%2Cw_750%2Climit_0)

reboot

![image.png](https://cdn.nlark.com/yuque/0/2023/png/35806411/1682319894823-7554469b-f33d-4126-bda7-16d03c5ba849.png)

## 2.Set hostname and hosts then reboot
```
vi /etc/hostname
change to edgenode1
vi /etc/hosts
add a new line
127.0.0.1 edgenode1
```
## 3.config network，install net-tools,wget,git
change the network config
```
cd /etc/sysconfig/network-scripts
vi ifcfg-enp0s3    #
#change ONBOOT=no to
ONBOOT=yes
#restart network
service network restart
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656315312730-08d044b0-04b4-4a93-abb9-243f09348ae3.png#averageHue=%23131313&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=177&id=u723c0b5b&margin=%5Bobject%20Object%5D&name=image.png&originHeight=265&originWidth=349&originalType=binary&ratio=1&rotation=0&showTitle=false&size=6580&status=done&style=none&taskId=u12129f07-80fe-401c-b19f-dc9aaae8c9d&title=&width=232.66666666666666)


```
## generate ssh key
```shell
ssh-keygen -t rsa -C "lihan@4paradigm.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:hThyKhL+StniqquROfAEC/w1CTxFmJ4iYVdRHJSBz3w lihan@4paradigm.com
The key's randomart image is:
+---[RSA 2048]----+
|  ..*=*=+        |
|o. *...+ .       |
|=oo ++B . .      |
|+=.o.+.= E       |
|=.+..   S        |
|.*+.             |
|=+.o             |
|ooo              |
|O+               |
+----[SHA256]-----+
```
## 4.remote ssh login
get the ip by ipconfig

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656315611850-11489855-8d8f-424f-9f22-a42e88501090.png#averageHue=%23121212&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=207&id=ub70f7d2e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=310&originWidth=713&originalType=binary&ratio=1&rotation=0&showTitle=false&size=13448&status=done&style=none&taskId=udd9e2679-38b8-4833-a80c-7e110cc8cc3&title=&width=475.3333333333333)

set the port forwarding on virtualbox node settings

![image.png](https://cdn.nlark.com/yuque/0/2023/png/35806411/1682320474516-118f5c4e-ff45-4f84-af71-69e84a266f39.png)


Add a new forwarding rule

![image.png](https://cdn.nlark.com/yuque/0/2023/png/35806411/1682320490526-27eb433e-8ce1-4dc1-a53e-418254858fc8.png)

then you can use ssh -p port  root@ip adress to login the node remotely

5）install docker

yum install docker
#add service to boot
systemctl enable docker
#docker comonands
service docker start
service docker stop
service docker restart

#set group driver
mkdir -p /etc/docker/
vi /etc/docker/daemon.json
{
"exec-opts": ["native.cgroupdriver=cgroupfs"] #for k3s, and for k8s it should be systemd
}

systemctl daemon-reload
systemctl restart docker
```
## 3.k3s deployment
  1）select version：

   [https://update.k3s.io/v1-release/channels](https://update.k3s.io/v1-release/channels)

2）install the k3s with offical documents

- install the k3s online
```shell
#close the firewall
systemctl stop firewalld
systemctl disable firewalld.service
#close SeLinux
setenforce 0
sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config
#close swap
swapoff -a    colse the swap temporarily
vim /etc/fstab  commemnt the line about swap，close the swap permanently
free check if the swap is closed


#install version 1.19 with docker，and disable traefik 
curl -sfL https://get.k3s.io  | INSTALL_K3S_MIRROR=cn INSTALL_K3S_VERSION=v1.19.16+k3s1 INSTALL_K3S_EXEC="--docker --disable traefik" sh -

[INFO]  Using v1.19.16+k3s1 as release
[INFO]  Downloading hash https://github.com/k3s-io/k3s/releases/download/v1.19.16+k3s1/sha256sum-amd64.txt
[INFO]  Downloading binary https://github.com/k3s-io/k3s/releases/download/v1.19.16+k3s1/k3s
[INFO]  Verifying binary download
[INFO]  Installing k3s to /usr/local/bin/k3s
Loading plugin：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
Package  yum-utils-1.1.31-54.el7_8.noarch installed and it is the latest version
Nothing to do
Loaded plugin：fastestmirror
Loaded plugin：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
rancher-k3s-common-stable                                                                     | 2.9 kB  00:00:00
rancher-k3s-common-stable/primary_db                                                          | 3.4 kB  00:00:02
Resolving Dependencies
--> Checking the trasaction test
---> Package k3s-selinux.noarch.0.1.1-1.el7 will be installed
--> Done

Dependencies Resolved

=====================================================================================================================
 Package                  type                version                      source                               size
=====================================================================================================================
Installing:
 k3s-selinux              noarch              1.1-1.el7                 rancher-k3s-common-stable               16 k

transaction  summary
=====================================================================================================================
Installed  1 package

total：16 k
installed：94 k
Downloading packages:
waring：/var/cache/yum/x86_64/7/rancher-k3s-common-stable/packages/k3s-selinux-1.1-1.el7.noarch.rpm: header V4 RSA/SHA1 Signature, key ID e257814a: NOKEY
k3s-selinux-1.1-1.el7.noarch.rpm public key is not installed
k3s-selinux-1.1-1.el7.noarch.rpm                                                              |  16 kB  00:00:03
From https://rpm.rancher.io/public.key 
Imrported GPG key 0xE257814A:
 User ID     : "Rancher (CI) <ci@rancher.com>"
 Fingerprint : c8cf f216 4551 26e9 b9c9 18be 925e a29a e257 814a
 From        : https://rpm.rancher.io/public.key
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing    : k3s-selinux-1.1-1.el7.noarch                                                                     1/1
  Checking      : k3s-selinux-1.1-1.el7.noarch                                                                     1/1

Installed:
  k3s-selinux.noarch 0:1.1-1.el7

Finished！
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


Set kubeconfig

```shell
vi /etc/profile
#add the following content
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml

source /etc/profile
systemctl enable k3s
```
Access the cluster from a remote machine

copythis file to ~/.kube/config 。
```shell
#check the key
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
