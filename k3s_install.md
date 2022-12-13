# 基础学习环境搭建
# 基础信息
机器节点（虚拟机virtualbox 6.1）：
操作系统：
* centos 7，下载地址：[http://mirrors.aliyun.com/centos/7/isos/x86_64/CentOS-7-x86_64-Minimal-2009.iso](http://mirrors.aliyun.com/centos/7/isos/x86_64/CentOS-7-x86_64-Minimal-2009.iso)
依赖服务：
* docker v20.10.17
* k3s 
* git
# 基础环境部署
以virtulbox为例：下一步。
## 0.设置virtualbox网络
新建natnetwork，所有使用此网络的虚拟机将会在一个局域网，且内部ip固定，不会因为宿主机换网络环境影响到虚拟机，比桥接模式更为优越。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656312830963-a13758b6-dc32-464a-9d88-6a01b68beb20.png#averageHue=%23f7f7f6&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=401&id=u02f12825&margin=%5Bobject%20Object%5D&name=image.png&originHeight=601&originWidth=949&originalType=binary&ratio=1&rotation=0&showTitle=false&size=35147&status=done&style=none&taskId=u975a2aa5-ce68-4951-9417-e01df91a012&title=&width=632.6666666666666)

该网络可以设置端口映射，方便将来外部电脑访问。
## 1.设置虚拟机并安装centos 
  1) 选择redhat64
  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656309361585-1ba0caea-a02c-4ecc-98ae-7fc9d60c6e2f.png#averageHue=%23f6f4f4&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=401&id=udc93cc94&margin=%5Bobject%20Object%5D&name=image.png&originHeight=602&originWidth=572&originalType=binary&ratio=1&rotation=0&showTitle=false&size=37790&status=done&style=none&taskId=uc6442286-b471-433d-9abd-d9140f1d887&title=&width=381.3333333333333)
 
  2）内存设置为2048M，内存可以后期动态调整。
  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656309564755-60613c7a-0673-4fa9-ab3f-0fbc784139aa.png#averageHue=%23fafafa&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=395&id=u181450c3&margin=%5Bobject%20Object%5D&name=image.png&originHeight=593&originWidth=567&originalType=binary&ratio=1&rotation=0&showTitle=false&size=18450&status=done&style=none&taskId=u3766b57a-3eb1-4ec6-aca5-bf88e92fce2&title=&width=378)

  3) 硬盘如图选择，硬盘大小设置为64G，后期可以通过新建硬盘，挂载。
  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656309666945-9ec3800b-16d8-40a0-b7df-0e8d1bc807ca.png#averageHue=%23f5f4f3&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=403&id=u4c235b1d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=605&originWidth=578&originalType=binary&ratio=1&rotation=0&showTitle=false&size=43299&status=done&style=none&taskId=u6662213f-4489-4320-9f47-fec68c2944e&title=&width=385.3333333333333)!

[image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656309758813-0dc12e45-2a9d-4273-a3c6-c217b1452690.png#averageHue=%23fafaf9&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=454&id=u7f249889&margin=%5Bobject%20Object%5D&name=image.png&originHeight=681&originWidth=612&originalType=binary&ratio=1&rotation=0&showTitle=false&size=31943&status=done&style=none&taskId=u308c212b-4b2b-4093-a7ff-c7e9956ae27&title=&width=408)![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656309803184-802755ad-1342-4d1c-9197-fbedcfc26218.png#averageHue=%23f8f7f7&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=457&id=uf892b553&margin=%5Bobject%20Object%5D&name=image.png&originHeight=686&originWidth=617&originalType=binary&ratio=1&rotation=0&showTitle=false&size=42047&status=done&style=none&taskId=u2c407c44-a10a-4eed-be74-3503c620e4a&title=&width=411.3333333333333)

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656309890604-6f7021de-a6ac-42db-9e86-0f0e5511c956.png#averageHue=%23f8f8f7&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=455&id=ud6660756&margin=%5Bobject%20Object%5D&name=image.png&originHeight=683&originWidth=628&originalType=binary&ratio=1&rotation=0&showTitle=false&size=33560&status=done&style=none&taskId=ua2382938-fd11-440b-a67f-510c27431b4&title=&width=418.6666666666667)

点击“创建”。

4）配置启动盘，选择设置，存储，选择盘片，添加下载好的os镜像文件，保存。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656310089069-b20c8490-8e83-470d-ba03-7670fc803662.png#averageHue=%23f4f3f3&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=581&id=uec674b6d&margin=%5Bobject%20Object%5D&name=image.png&originHeight=872&originWidth=1196&originalType=binary&ratio=1&rotation=0&showTitle=false&size=67990&status=done&style=none&taskId=u1f46f7d6-43f7-46a6-ab2a-156fffce039&title=&width=797.3333333333334)
选择NAT网络：

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656312520684-9b324158-b60f-4e6c-b357-406db7cba01b.png#averageHue=%23f7f6f6&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=594&id=ub63188ef&margin=%5Bobject%20Object%5D&name=image.png&originHeight=891&originWidth=1200&originalType=binary&ratio=1&rotation=0&showTitle=false&size=58928&status=done&style=none&taskId=u31248c98-ac7f-41d5-ad4d-8ff454ed2a6&title=&width=800)

5）选择刚才创建的虚拟机，点击“启动”

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656310593568-4129fbd7-0053-44df-adff-11ff6ed9c4c0.png#averageHue=%23e7e4e2&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=397&id=uee8b3a54&margin=%5Bobject%20Object%5D&name=image.png&originHeight=595&originWidth=1168&originalType=binary&ratio=1&rotation=0&showTitle=false&size=112663&status=done&style=none&taskId=u499a44ae-9c78-4622-9360-27c53809370&title=&width=778.6666666666666)

默认选择"Test this media & install CentOS 7",回车。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656310647389-ad783998-5fb4-4aba-b20c-0954abbc37d5.png#averageHue=%23313131&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=405&id=ub5814d80&margin=%5Bobject%20Object%5D&name=image.png&originHeight=608&originWidth=644&originalType=binary&ratio=1&rotation=0&showTitle=false&size=27657&status=done&style=none&taskId=ued1d0ae0-a332-4ea3-b789-266e3cbb83a&title=&width=429.3333333333333)

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656310891990-7efcbf8d-18fd-4685-a724-ebb15be0613d.png#averageHue=%23e7e7e5&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=475&id=u4433ca8a&margin=%5Bobject%20Object%5D&name=image.png&originHeight=713&originWidth=800&originalType=binary&ratio=1&rotation=0&showTitle=false&size=137921&status=done&style=none&taskId=uc84e162f-cb64-4798-8b88-a495a68d860&title=&width=533.3333333333334)

下一步，完成各项系统设置，选择时区：

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656311009213-8faaa209-bd05-49ed-aee7-a622e3c84c6d.png#averageHue=%23e8e3db&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=480&id=u99a94ea4&margin=%5Bobject%20Object%5D&name=image.png&originHeight=720&originWidth=799&originalType=binary&ratio=1&rotation=0&showTitle=false&size=135655&status=done&style=none&taskId=u8fe153da-284a-49f6-9135-a2b3fc896cc&title=&width=532.6666666666666)

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656310963481-29371d0d-e6b5-4e0b-929b-d700ddcb2ab9.png#averageHue=%23a5d480&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=478&id=u6e5f9344&margin=%5Bobject%20Object%5D&name=image.png&originHeight=717&originWidth=801&originalType=binary&ratio=1&rotation=0&showTitle=false&size=198014&status=done&style=none&taskId=u89cc24d2-e3eb-4555-aca7-1066cc6705a&title=&width=534)

system中installation destiontion进入默认选择。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656311125609-09fdbe23-a0f8-48bd-aef0-1152d28b8f77.png#averageHue=%23e7e7e5&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=477&id=u7d4fd5fa&margin=%5Bobject%20Object%5D&name=image.png&originHeight=716&originWidth=798&originalType=binary&ratio=1&rotation=0&showTitle=false&size=139782&status=done&style=none&taskId=ub4a48332-5339-4be8-928b-c4923884c4f&title=&width=532)

其他不需要配置，点击“begin installation”。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656311475385-33db23ee-d279-456b-9bdd-4bd228fc5286.png#averageHue=%23e4e4e2&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=479&id=u5c8efec4&margin=%5Bobject%20Object%5D&name=image.png&originHeight=719&originWidth=800&originalType=binary&ratio=1&rotation=0&showTitle=false&size=185142&status=done&style=none&taskId=u2f70900c-ac0f-4f66-91f9-ea51e053042&title=&width=533.3333333333334)

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656311341152-c09dbd10-54fd-4cc0-b748-96b90268a701.png#averageHue=%23e8e3db&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=428&id=u5eacb30c&margin=%5Bobject%20Object%5D&name=image.png&originHeight=642&originWidth=808&originalType=binary&ratio=1&rotation=0&showTitle=false&size=61416&status=done&style=none&taskId=u5d57605d-d2b3-48f4-aed0-c568e2b293e&title=&width=538.6666666666666)

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656312034119-695647a9-7246-4b2d-aaeb-374a6c1230f7.png#averageHue=%23e9e4dd&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=485&id=uedb7a537&margin=%5Bobject%20Object%5D&name=image.png&originHeight=728&originWidth=808&originalType=binary&ratio=1&rotation=0&showTitle=false&size=111218&status=done&style=none&taskId=uad2ba0ca-fbdd-43b7-a8df-e0fc309aab4&title=&width=538.6666666666666)

点击reboot，重启安装完成。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656312108091-a4193994-8c29-470c-9325-bbbd07bf99a2.png#averageHue=%232a2a2a&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=487&id=u0fa690af&margin=%5Bobject%20Object%5D&name=image.png&originHeight=730&originWidth=806&originalType=binary&ratio=1&rotation=0&showTitle=false&size=24800&status=done&style=none&taskId=u7845f73e-83e0-42a7-aa8c-ac442903689&title=&width=537.3333333333334)

## 2.设置hostname及hosts，重启
```
vi /etc/hostname
修改为edgenode1，wq退出
vi /etc/hosts
增加一行
127.0.0.1 edgenode1
```
## 3.配置网络，安装net-tools,wget,ntpdate,git
修改网络配置，使得其能上网，如图
```
cd /etc/sysconfig/network-scripts
vi ifcfg-enp0s3    #后缀可能不完全相同
#修改最后一行改为yes
ONBOOT=yes
#重启网络
service network restart
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656315312730-08d044b0-04b4-4a93-abb9-243f09348ae3.png#averageHue=%23131313&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=177&id=u723c0b5b&margin=%5Bobject%20Object%5D&name=image.png&originHeight=265&originWidth=349&originalType=binary&ratio=1&rotation=0&showTitle=false&size=6580&status=done&style=none&taskId=u12129f07-80fe-401c-b19f-dc9aaae8c9d&title=&width=232.66666666666666)

安装软件并修改yum源。
```
yum -y install net-tools wget ntpdate git gcc
ntpdate time1.aliyun.com
# 删除本地时间并设置时区为上海（如果已经是上海时间可以跳过次步）
rm -rf /etc/localtime
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
# 查看时间
date -R || date
```
## 生成ssh 密钥
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
## 4.配置ssh远程登录
通过ifconfig获得ip

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656315611850-11489855-8d8f-424f-9f22-a42e88501090.png#averageHue=%23121212&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=207&id=ub70f7d2e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=310&originWidth=713&originalType=binary&ratio=1&rotation=0&showTitle=false&size=13448&status=done&style=none&taskId=udd9e2679-38b8-4833-a80c-7e110cc8cc3&title=&width=475.3333333333333)

进入virtualbox管理->全局设定->网络

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656315653988-5473d3ab-71de-4e77-9468-a19bcd8adca6.png#averageHue=%23f8f5f3&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=387&id=ue1745cc1&margin=%5Bobject%20Object%5D&name=image.png&originHeight=581&originWidth=923&originalType=binary&ratio=1&rotation=0&showTitle=false&size=87147&status=done&style=none&taskId=u0f1fef66-56a2-4e68-aa9b-857cd5140ca&title=&width=615.3333333333334)

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656315727649-b62064dd-e8fe-4a91-b12e-3d29216143ac.png#averageHue=%23f8f8f8&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=359&id=u24803153&margin=%5Bobject%20Object%5D&name=image.png&originHeight=538&originWidth=959&originalType=binary&ratio=1&rotation=0&showTitle=false&size=36971&status=done&style=none&taskId=u5dc08d17-9da7-469b-8d6e-b1b600207b0&title=&width=639.3333333333334)

点击红框，编辑网络->端口转发，添加一条，保存。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/28211224/1656315822547-f7ed1c1d-006a-4b6a-909c-ee1af34d30d1.png#averageHue=%23f5f1f0&clientId=u6ad16729-0251-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=380&id=ua8da9e4e&margin=%5Bobject%20Object%5D&name=image.png&originHeight=570&originWidth=932&originalType=binary&ratio=1&rotation=0&showTitle=false&size=53504&status=done&style=none&taskId=uea7e1155-00d1-4a05-9d37-2220cab417e&title=&width=621.3333333333334)

这样就可以通过ssh -p 7777  root@10.100.116.172远程管理该节点了。

5）设置yum源为阿里源
```json
# 备份默认的yum
mv /etc/yum.repos.d /etc/yum.repos.d.backup
# 设置新的yum目录
mkdir -p /etc/yum.repos.d
# 下载阿里yum配置到该目录中，选择对应版本
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
yum clean all
yum makecache
yum update
```
# 开发环境部署
## 0.docker部署
```shell
#安装yum-utils
yum install -y yum-utils device-mapper-persistent-data lvm2
#添加阿里源
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
#列出所有docker版本
yum list docker-ce --showduplicates | sort -r
#安装最新稳定版本
yum install docker-ce
#添加自启动
systemctl enable docker
#docker启动停止重启命令
service docker start
service docker stop
service docker restart

#配置代理和模式
mkdir -p /etc/docker/
vi /etc/docker/daemon.json
{
"registry-mirrors": [
  "https://registry.cn-hangzhou.aliyuncs.com",
  "http://hub-mirror.c.163.com"
  ],
"exec-opts": ["native.cgroupdriver=cgroupfs"] #k3s要求，对于k8s应该为systemd
}

systemctl daemon-reload
systemctl restart docker
```
## 3.k3s部署
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

  1. 在[releases](https://github.com/rancher/k3s/releases)下载k3s及k3s-airgap-images-amd64.tar。
  2. 将k3s文件移动到**/usr/local/bin目录下，并chmod +x /usr/local/bin/k3s
  3. 将k3s-airgap-images-amd64.tar移动到**/var/lib/rancher/k3s/agent/images/**
  4. 下载安装脚本
     ```
     wget https://get.k3s.io -O install.sh,
     chmod +x install.sh
     ```
  5. 执行INSTALL_K3S_SKIP_DOWNLOAD=true INSTALL_K3S_EXEC="--docker --disable traefik"  ./install.sh

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
