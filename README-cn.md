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

* kubernetes 最低v1.16.8+,推荐v1.19.16+
* k3s(个人学习使用)

> [k3s安装参考](./k3s_install-cn.md)

* mysql/mariadb 5.7+

#### 依赖镜像

* flowengine/flowengine-ui:0.0.3.4
* flowengine/engine-manager:0.0.3.5
* flowengine/data:0.0.3.4
* flowengine/hub:0.0.3.4
* flowengine/engine-kernel:0.0.3.5（有机器学习需要的可以下载flowengine/engine-kernel:0.0.3.5-ml，也可以自己根据文档定制）
* flowengine/func-pipeline-runtime:0.0.3
* traefik:v2.5

### 操作步骤

> 若无法连接中央镜像库，需确保将系统依赖的镜像上传到私有镜像仓库，并保证与K8s deploy yaml中镜像地址保持一致。
> 将本repo下载或者clone到本地，进入该工作目录

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
执行`3-fl-gateway.yaml`，老版本k8s（<=1.18）执行`3-fl-gateway-k8s-below-1.18.yaml`

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

> <span style="color:red"> 修改cm，需要delete pod重启，服务配置才能生效</span>

* 数据库配置

```...
 server.tomcat.accesslog.request-attributes-enabled=true

 db.host=localhost #修改为你的mysql地址
 db.port=3306 #修改为你的mysql地址
 db.name=fl_engine_manager #保持不变
 db.username=root #修改为你的mysql地址
 db.password= #修改为你的mysql地址

 flyway.enabled=true   #系统会自动建库建表
 ....
```

> 对于安装有helm工具的k8s集群，可以通过chart安装mysql，mysql默认为my-mariadb.flowengine：

```
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install --namespace flowengine my-mariadb bitnami/mariadb --version 11.1.1
#获得密码
kubectl get secret --namespace flowengine my-mariadb -o jsonpath="{.data.mariadb-root-password}" | base64 -d
# 1. Run a pod that you can use as a client:
kubectl run my-mariadb-client --rm --tty -i --restart='Never' --image  docker.io/bitnami/mariadb:10.6.8-debian-11-r9 --namespace flowengine --command -- bash
#2. To connect to primary service (read/write):
 mysql -h my-mariadb.flowengine.svc.cluster.local -uroot -p my_database
#3. 修改密码
 SET PASSWORD FOR 'root'@'%' = PASSWORD('123456');
 参考此文档 https://mariadb.org/mariadb-k8s-create-a-secret-and-use-it-in-mariadb-deployment/
 创建secret: 
 echo -n '123456' >./password.txt 
 kubectl create secret generic mariadb-secret  --from-file=mariadb-root-password=./password.txt -n flowengine
 修改statefuset配置kubectl edit statefulset my-mariadb -n flowengine，绑定
 - name: MARIADB_ROOT_PASSWORD
   valueFrom:
     secretKeyRef:
       key: mariadb-root-password
       name: mariadb-secret
 如果未操作此步，会出现mariadb循环重启的现象。
```

* 工作区配置

可以通过以下配置创建flowengine管理的ns，创建fl-ns.yaml,内容如下：

```
apiVersion: v1
kind: Namespace
metadata:
  annotations:
    fl-managed-ns-config: '{"onAios":false,"name":"fl-test","aiosWorkspaceId":"","aiosAccessKey":""}'
  labels:
    fl-managed-ns: "true"
  name: fl-test
```

执行`kubectl apply -f fl-ns.yaml`即可创建。
> 也可直接使用examples目录里的fl-test-workspace.yaml创建。

实际上，你也可以通过在已有ns里增加上述annotation和labels来将其交给flowengine托管。


至此，flowengine已安装完成，管理后台地址：http://{entry_ip}:31101，用户名密码admin/admin，enjoy it！

> flowengine默认地址为
>
> ```
> |------------|------------|-----------------|---------------------------|
> | NAMESPACE  |    NAME    |   TARGET PORT   |            URL            |
> |------------|------------|-----------------|---------------------------|
> | flowengine | fl-traefik | flowengine/9001 | http://192.168.49.2:31101 |
> |            |            | traefik/9002    | http://192.168.49.2:31102 |
> |            |            | fl-online/80    | http://192.168.49.2:31100 |
> |------------|------------|-----------------|---------------------------|
> ```

* 导入或者创建方案创建引擎。
  在创建引擎之前，确保hub中有要选择的方案，并且方案状态处于"已发布"状态。如果不会创建方案，导入example中的默认asol，创建方案。

>  注意，方案中默认设置了引擎依赖的数据库配置，可以酌情修改，如果想要全局对接数据库，可在"设定"->"数据库配置信息"中:
> "数据库默认开关"设置为false，数据库相关参数设置为实际配置。
>
> 对于低版本k8s(<=1.18)，还需要执行如下sql，保证启动的engine兼容ingress，更新sql完成后，重启fl-engine-manager服务。
>
> ```
> delete from automl_manager_dict_value where dict_id = '4';
> INSERT INTO `automl_manager_dict_value` (`id`, `dict_id`, `value_name`, `value_code`, `value`, `remark`, `value_order`)
> VALUES
>    (10, 4, 'enginekernel ingress template', 'engine_kernel_ingress_template', '{\n \"apiVersion\": \"extensions/v1beta1\",\n  \"kind\": \"Ingress\",\n  \"metadata\": {\n   \"annotations\": {\n      \"traefik.ingress.kubernetes.io/router.middlewares\": \"flowengine-fl-stripurl@kubernetescrd\",\n     \"traefik.ingress.kubernetes.io/router.entrypoints\": \"flowengine\"\n    },\n    \"labels\": {\n     \"app\": \"fl-traefik\"\n   },\n    \"name\": \"${ingressName}\"\n  },\n  \"spec\": {\n   \"rules\": [{\n     \"http\": {\n       \"paths\": [{\n           \"backend\": {\n              \"serviceName\": \"${serviceName}\",\n              \"servicePort\": ${servicePort}\n           },\n            \"path\": \"/engine/${relModuleId}/${engineKey}/\"\n          },\n          {\n           \"backend\": {\n              \"serviceName\": \"${serviceName}\",\n              \"servicePort\": ${servicePort}\n             },\n            \"path\": \"/engine/${moduleId}/\"\n          }\n       ]\n     }\n   }]\n  }\n}', NULL, 1),
>    (11, 4, 'scIngressTemplate', 'sc_ingress_template', '{\n  \"apiVersion\": \"extensions/v1beta1\",\n  \"kind\": \"Ingress\",\n  \"metadata\": {\n   \"annotations\": {\n      \"traefik.ingress.kubernetes.io/router.entrypoints\": \"flowengine\",\n     \"traefik.ingress.kubernetes.io/router.middlewares\": \"flowengine-fl-stripurl@kubernetescrd\"\n    },\n    \"labels\": {\n     \"app\": \"fl-traefik\"\n   },\n    \"name\": \"${ingressName}\"\n  },\n  \"spec\": {\n   \"rules\": [{\n     \"http\": {\n       \"paths\": [{\n         \"backend\": {\n            \"serviceName\": \"${serviceName}\",\n            \"servicePort\": ${servicePort}\n         },\n          \"path\": \"/engine/${engineKey}/${moduleId}/${scId}\"\n       }, {\n          \"backend\": {\n            \"serviceName\": \"${serviceName}\",\n            \"servicePort\": ${servicePort}\n            },\n          \"path\": \"/engine/${scKey}/${moduleId}/${scId}\"\n       }]\n      }\n   }]\n  }\n}', NULL, 2),
>    (12, 4, 'traefikOnlineConfig', 'traefik_online_template', '{\n  \"apiVersion\": \"extensions/v1beta1\",\n \"kind\": \"Ingress\",\n  \"metadata\": {\n   \"annotations\": {\n      \"traefik.ingress.kubernetes.io/router.entrypoints\": \"fl-online\",\n      \"traefik.ingress.kubernetes.io/router.middlewares\": \"flowengine-fl-stripurl@kubernetescrd\"\n    },\n    \"labels\": {\n     \"app\": \"fl-traefik\"\n   },\n    \"name\": \"${ingressName}\"\n  },\n  \"spec\": {\n   \"rules\": [{\n     \"http\": {\n       \"paths\": [{\n         \"backend\": {\n            \"serviceName\": \"${serviceName}\",\n             \"servicePort\":  ${servicePort}\n         },\n          \"path\": \"${gatewayUri}\"\n       }]\n      }\n   }]\n  }\n}', NULL, 3);
> ```

到此，你就可以创建自己的引擎，进行更深入探索了。
具体使用方法可参考官方文档:https://flow-engine.github.io/docs/

### 更新日志
0.0.3.5（2024.1.30）
* 修复了并行job执行完成后资源未完全清理的问题

0.0.3.4（2023.4.28）
* 统一了全站url，便于后续迭代
* 修复了部份使用性问题
（本版本需要全部安装文档执行一次）

0.0.3.2(2023.3.20)：
* 修复了一些使用性问题。
* 数据库名统一，并清理了无用的数据。
* 增加了并行任务编排的beta版本功能
