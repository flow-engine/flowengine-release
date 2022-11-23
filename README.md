# 快速安装体验指南

### 组成
* fl-engine-manager
  * service:
  * deploy
  * configmap
* fl-hub 
  * service
  * deploy
  * configmap
* fl-data
  * service
  * deploy
  * configmap
* fl-traefik
  * ingress
  * deploy
  * service
  * middleware
### 前置依赖
#### 安装环境
* kubernetes v1.16.8+
* minikube v1.23.0+(个人学习使用)
> 使用kind（https://kind.sigs.k8s.io/）或者k3s
> 对于kind和minikube注意如果是外部导入镜像，应手工导入到集群内部
> (kind:https://kind.sigs.k8s.io/docs/user/quick-start/#loading-an-image-into-your-cluster）
> (minikube:https://www.liujiajia.me/2022/5/28/manual-import-image-to-minikube)
> k3s安装参考
* mysql 5.7+
#### 依赖镜像
* flowengine
* traefik

### 操作步骤
> 确保将系统依赖的镜像上传到镜像仓库，并保证与K8s deploy yaml中镜像地址保持一致。

以下操作在kubernetes命令行或者能够执行k8s命令的窗口执行。

1. 添加traefik CRD
```
(base) ➜  flowengine-release git:(master) ✗ ls
README.md config    examples  images    tests
(base) ➜  flowengine-release git:(master) ✗ kubectl apply -f config/1-fl-traefik-crd.yml
customresourcedefinition.apiextensions.k8s.io/ingressroutes.traefik.containo.us configured
customresourcedefinition.apiextensions.k8s.io/ingressroutetcps.traefik.containo.us configured
customresourcedefinition.apiextensions.k8s.io/ingressrouteudps.traefik.containo.us configured
customresourcedefinition.apiextensions.k8s.io/middlewares.traefik.containo.us configured
customresourcedefinition.apiextensions.k8s.io/middlewaretcps.traefik.containo.us configured
customresourcedefinition.apiextensions.k8s.io/serverstransports.traefik.containo.us configured
customresourcedefinition.apiextensions.k8s.io/tlsoptions.traefik.containo.us configured
customresourcedefinition.apiextensions.k8s.io/tlsstores.traefik.containo.us configured
customresourcedefinition.apiextensions.k8s.io/traefikservices.traefik.containo.us configured
clusterrole.rbac.authorization.k8s.io/fl-traefik-ingress-controller created
clusterrolebinding.rbac.authorization.k8s.io/fl-traefik-ingress-controller created
(base) ➜  flowengine-release git:(master) ✗
```
2. 部署flowengine

在k8s中创建flowengine namespace

```
(base) ➜  flowengine-release git:(master) ✗ kubectl create ns flowengine
namespace/flowengine created
```
安装flowengine,执行前，请先确认镜像和数据库地址都填写正确。
```
(base) ➜  flowengine-release git:(master) ✗ kubectl apply -f config/2-fl.yaml
serviceaccount/fl-enginemanager created
clusterrole.rbac.authorization.k8s.io/fl-enginemanager-role unchanged
clusterrolebinding.rbac.authorization.k8s.io/fl-enginemanager-role-binding unchanged
configmap/config-fl-data created
configmap/config-elf-flowengine-bundled created
configmap/config-fl-engine-manager created
configmap/config-fl-hub created
deployment.apps/elf-flowengine-bundled created
deployment.apps/fl-engine-manager created
deployment.apps/fl-data created
deployment.apps/fl-hub created
service/elf-flowengine-bundled created
service/fl-engine-manager created
service/fl-data created
service/fl-hub created
   ```

安装flowengine网关
执行`3-fl-gateway.yaml`
```
(base) ➜  flowengine-release git:(master) ✗ kubectl apply -f config/3-fl-gateway.yaml
deployment.apps/fl-traefik created
ingress.networking.k8s.io/flowengine-gateway created
service/fl-traefik created
serviceaccount/fl-traefik-ingress-controller created
middleware.traefik.containo.us/fl-stripurl created
```

检查任务启动情况，结合错误，修改相关cm配置。

```
(base) ➜  flowengine-release git:(master) ✗ kubectl get pod -n flowengine
NAME                                      READY   STATUS              RESTARTS   AGE
elf-flowengine-bundled-6c6bb78945-s88hr   0/1     ContainerCreating   0          4m52s
fl-data-67ff5bc686-6g9rz                  0/1     ContainerCreating   0          4m51s
fl-engine-manager-85b8d7b7c-zz4p2         0/1     ContainerCreating   0          4m52s
fl-hub-fdcb64dcf-twgzk                    0/1     ContainerCreating   0          4m51s
fl-traefik-6b4ff8d568-xhxnr               0/1     ContainerCreating   0          50s
```
如上，观察启动错误，根据提示修改，直到运行正常。



### 常见修改项
flowengine启动配置一般在configmap中修改。如下：
```  
(base) ➜  flowengine-release git:(master) ✗ kubectl get cm -n flowengine
   NAME                            DATA   AGE
   config-elf-flowengine-bundled   2      113m
   config-fl-data                  1      113m
   config-fl-engine-manager        1      113m
   config-fl-hub                   1      113m
   kube-root-ca.crt                1      113m
``` 
><span style="color:red"> 修改cm，需要delete pod重启，服务配置才能生效</span>
* 数据库配置

 ```...
  server.tomcat.accesslog.request-attributes-enabled=true

  db.host=localhost #修改为你的mysql地址
  db.port=3306 #修改为你的mysql地址
  db.name=engine-manager #保持不变
  db.username=root #修改为你的mysql地址
  db.password= #修改为你的mysql地址

  flyway.enabled=true   #系统会自动建库建表
  ....
 ```
> 注意有些版本的mysql不兼容数据库名存在"-"，应先在数据库中通过
``create database `engine-manager`;create database `solution-market` ``手工创建
> 对于安装有helm工具的k8s集群，可以通过chart安装mysql：
```
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-mariadb bitnami/mariadb --version 11.1.1
```

可以通过以下配置创建flowengine管理的ns，创建fl-ns.yaml,内容如下：
```
apiVersion: v1
kind: Namespace
metadata:
  annotations:
    fl-managed-ns-config: '{"onAios":false,"name":"fl-test","aiosAccessKey":""}'
  labels:
    fl-managed-ns: "true"
  name: fl-test
```
执行`kubectl apply -f fl-ns.yaml`即可创建。
实际上，你也可以通过在已有ns里增加上述annotation和labels来将其交给flowengine托管。

至此，flowengine已安装完成，用户名密码admin/admin，enjoy it！
> flowengine默认地址为
> ```
> |------------|------------|-----------------|---------------------------|
> | NAMESPACE  |    NAME    |   TARGET PORT   |            URL            |
> |------------|------------|-----------------|---------------------------|
> | flowengine | fl-traefik | flowengine/9001 | http://192.168.49.2:31101 |
> |            |            | traefik/9002    | http://192.168.49.2:31102 |
> |            |            | fl-online/80    | http://192.168.49.2:31100 |
> |------------|------------|-----------------|---------------------------|
> ```
> 如果是minikube启动，请使用：
> ` minikube service fl-traefik -n flowengine` 映射本地地址，登陆访问。

> 注意，创建引擎的方案可使用该repo中examples目录的方案，另外引擎绑定的数据库地址应在"设定"->"数据库配置信息"中:
> "数据库默认开关"设置为false，数据库相关参数设置为实际配置。






