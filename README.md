# Quick install guide
English ｜ [中文](/README-cn.md)

### MODULES

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

### Pre-dependencies

#### Install the library

* kubernetes higher than v1.16.8+, Recommended Version is higher than v1.19.16+
* k3s(for local development)

> [k3s Install Guide for local development](./k3s_install.md)

* mysql/mariadb 5.7+

#### Images

* flowengine/flowengine-ui:0.0.3.2
* flowengine/engine-manager:0.0.3.2-beta2
* flowengine/data:0.0.2
* flowengine/hub:0.0.3.2-beta2
* flowengine/engine-kernel:0.0.3.2-beta2（You can use flowengine/engine-kernel:0.0.3.2-ml2 for machine learning，and you can also check the documents for more details）
* flowengine/func-pipeline-runtime:0.0.2
* traefik:v2.5

### Instructions



Need K8S command line tool `kubectl` and `helm` installed.

1. Add traefik CRD

```
Check out flowengine-release git:(master) 
# cd flowengine-release
flowengine-release# ls
README.md config    examples  images    tests
flowengine-release# kubectl apply -f config/1-fl-traefik-crd.yml
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
```

2. Deploy flowengine

Create flowengine namespace

```
flowengine-release# kubectl create ns flowengine
namespace/flowengine created
```

Install flowengine

```
flowengine-release# kubectl apply -f config/2-fl.yaml
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

Install flowengine Gateway
K8s/kubectl version higher than 1.18 apply `3-fl-gateway.yaml`else apply`3-fl-gateway-k8s-below-1.18.yaml`

```
flowengine-release# kubectl apply -f config/3-fl-gateway.yaml
deployment.apps/fl-traefik created
ingress.networking.k8s.io/flowengine-gateway created
service/fl-traefik created
serviceaccount/fl-traefik-ingress-controller created
middleware.traefik.containo.us/fl-stripurl created
```

Check the pods status

```
flowengine-release# kubectl get pod -n flowengine
NAME                                      READY   STATUS              RESTARTS   AGE
elf-flowengine-bundled-6c6bb78945-s88hr   0/1     ContainerCreating   0          4m52s
fl-data-67ff5bc686-6g9rz                  0/1     ContainerCreating   0          4m51s
fl-engine-manager-85b8d7b7c-zz4p2         0/1     ContainerCreating   0          4m52s
fl-hub-fdcb64dcf-twgzk                    0/1     ContainerCreating   0          4m51s
fl-traefik-6b4ff8d568-xhxnr               0/1     ContainerCreating   0          50s
```

Check the service status, if there are any issues please fix it or ask in the community, we will help you.

```

### You can check the following list for the addtional changes

flowengine the initial configurtation in configmap

```
flowengine-release#  kubectl get cm -n flowengine
   NAME                            DATA   AGE
   config-elf-flowengine-bundled   2      113m
   config-fl-data                  1      113m
   config-fl-engine-manager        1      113m
   config-fl-hub                   1      113m
   kube-root-ca.crt                1      113m
```

> <span style="color:red"> If you want to change the configuration of cm，you need delete pod and restart, then the new configruration will be worked</span>

* Database confirguration

```

```...
 server.tomcat.accesslog.request-attributes-enabled=true

 db.host=localhost #You can change to your mysql address
 db.port=3306 #You can change to your mysql port
 db.name=fl_engine_manager #Please don't change it
 db.username=root #You can change to your mysql username
 db.password= #You can change to your mysql password

 flyway.enabled=true   #If you want to use flyway to init the database, please set true
 ....
```

> if you installed the helm,you can get mysql database by charts，the default mysql is my-mariadb.flowengine：

```
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install --namespace flowengine my-mariadb bitnami/mariadb --version 11.1.1
#Get the default password
kubectl get secret --namespace flowengine my-mariadb -o jsonpath="{.data.mariadb-root-password}" | base64 -d
 1. Run a pod that you can use as a client:
 # kubectl run my-mariadb-client --rm --tty -i --restart='Never' --image  docker.io/bitnami/mariadb:10.6.8-debian-11-r9 --namespace flowengine --command -- bash
 my-mariadb-client# 
 
 2. To connect to primary service (read/write):
 my-mariadb-client# mysql -h my-mariadb.flowengine.svc.cluster.local -uroot -p my_database
 mysql#
 
 3. Change the password to 123456, then the flowengine can connect to the database
 mysql# SET PASSWORD FOR 'root'@'%' = PASSWORD('123456');
 mysql# exit
 my-mariadb-client# exit
 #
 
 The reference document is https://mariadb.org/mariadb-k8s-create-a-secret-and-use-it-in-mariadb-deployment/
 
 create secret: 
 # echo -n '123456' >./password.txt 
 # kubectl create secret generic mariadb-secret  --from-file=mariadb-root-password=./password.txt -n flowengine
 
Please change the statefuset
 # kubectl edit statefulset my-mariadb -n flowengine
 - name: MARIADB_ROOT_PASSWORD
   valueFrom:
     secretKeyRef:
       key: mariadb-root-password
       name: mariadb-secret
 > <span style="color:red"> If you didn't do this，the mariadb will be crashed</span>
```

* The workspace configuration

```

You can create the ns managed by flowengine via fl-ns.yaml:

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

# kubectl apply -f fl-ns.yaml
> You can also use the fl-test-workspace.yaml under the flowengine-release/examples

And you can add annotation and labels in exsiting ns for flowengine to mange it.

```

The admin portal of flowengine http://{entry_ip}:31101 admin/admin enjoy it！

> Default address of flowengine is
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

* Import or create solution engine

```
Before  you create engine，please select a solution in hub，and the status of solution is released, or you can import the defualt asol under flowengine-release/example to create solutions


>
>  If k8s/kubectl version lower than 1.18，need to trigger the following sql，then restart fl-engine-manager service
>
> ```
> delete from automl_manager_dict_value where dict_id = '4';
> INSERT INTO `automl_manager_dict_value` (`id`, `dict_id`, `value_name`, `value_code`, `value`, `remark`, `value_order`)
> VALUES
>    (10, 4, 'enginekernel ingress template', 'engine_kernel_ingress_template', '{\n \"apiVersion\": \"extensions/v1beta1\",\n  \"kind\": \"Ingress\",\n  \"metadata\": {\n   \"annotations\": {\n      \"traefik.ingress.kubernetes.io/router.middlewares\": \"flowengine-fl-stripurl@kubernetescrd\",\n     \"traefik.ingress.kubernetes.io/router.entrypoints\": \"flowengine\"\n    },\n    \"labels\": {\n     \"app\": \"fl-traefik\"\n   },\n    \"name\": \"${ingressName}\"\n  },\n  \"spec\": {\n   \"rules\": [{\n     \"http\": {\n       \"paths\": [{\n           \"backend\": {\n              \"serviceName\": \"${serviceName}\",\n              \"servicePort\": ${servicePort}\n           },\n            \"path\": \"/automl-engine/${relModuleId}/${engineKey}/\"\n          },\n          {\n           \"backend\": {\n              \"serviceName\": \"${serviceName}\",\n              \"servicePort\": ${servicePort}\n             },\n            \"path\": \"/automl-engine/${moduleId}/\"\n          }\n       ]\n     }\n   }]\n  }\n}', NULL, 1),
>    (11, 4, 'scIngressTemplate', 'sc_ingress_template', '{\n  \"apiVersion\": \"extensions/v1beta1\",\n  \"kind\": \"Ingress\",\n  \"metadata\": {\n   \"annotations\": {\n      \"traefik.ingress.kubernetes.io/router.entrypoints\": \"flowengine\",\n     \"traefik.ingress.kubernetes.io/router.middlewares\": \"flowengine-fl-stripurl@kubernetescrd\"\n    },\n    \"labels\": {\n     \"app\": \"fl-traefik\"\n   },\n    \"name\": \"${ingressName}\"\n  },\n  \"spec\": {\n   \"rules\": [{\n     \"http\": {\n       \"paths\": [{\n         \"backend\": {\n            \"serviceName\": \"${serviceName}\",\n            \"servicePort\": ${servicePort}\n         },\n          \"path\": \"/automl-engine/${engineKey}/${moduleId}/${scId}\"\n       }, {\n          \"backend\": {\n            \"serviceName\": \"${serviceName}\",\n            \"servicePort\": ${servicePort}\n            },\n          \"path\": \"/automl-engine/${scKey}/${moduleId}/${scId}\"\n       }]\n      }\n   }]\n  }\n}', NULL, 2),
>    (12, 4, 'traefikOnlineConfig', 'traefik_online_template', '{\n  \"apiVersion\": \"extensions/v1beta1\",\n \"kind\": \"Ingress\",\n  \"metadata\": {\n   \"annotations\": {\n      \"traefik.ingress.kubernetes.io/router.entrypoints\": \"fl-online\",\n      \"traefik.ingress.kubernetes.io/router.middlewares\": \"flowengine-fl-stripurl@kubernetescrd\"\n    },\n    \"labels\": {\n     \"app\": \"fl-traefik\"\n   },\n    \"name\": \"${ingressName}\"\n  },\n  \"spec\": {\n   \"rules\": [{\n     \"http\": {\n       \"paths\": [{\n         \"backend\": {\n            \"serviceName\": \"${serviceName}\",\n             \"servicePort\":  ${servicePort}\n         },\n          \"path\": \"${gatewayUri}\"\n       }]\n      }\n   }]\n  }\n}', NULL, 3);
> ```


Our document is here :https://flow-engine.github.io/docs/