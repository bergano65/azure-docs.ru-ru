---
title: Установка существующих приложений с шлемом в AKS
description: Узнайте, как использовать инструмент упаковки Helm для развертывания контейнеров в кластере Службы Azure Kubernetes (AKS)
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/22/2019
ms.author: zarhoads
ms.openlocfilehash: e46bed5fc9fd83a907f8c9e716317a54548c58cc
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870254"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Установка существующих приложений с помощью службы «Хелм» в Azure Kubernetes Service (AKS)

[Helm][helm] — это средство упаковки с открытым кодом, которое помогает установить приложения Kubernetes и управлять их жизненным циклом. Аналогично диспетчерам пакетов Linux, таких как *APT* и *Yum*, Helm используется для управления чартами Kubernetes, представляющими собой пакеты предварительно настроенных ресурсов Kubernetes.

В этой статье показано, как настроить и использовать Helm в кластере Kubernetes в AKS.

## <a name="before-you-begin"></a>Перед началом

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, обратитесь к этому краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

Вам также необходимо установить Helm CLI, который является клиентом, который работает на вашей системе разработки. Это позволяет запускать, останавливать и управлять приложениями с помощью Helm. Если вы используете Azure Cloud Shell, интерфейс командной строки Helm уже установлен. Для инструкций по установке на локальной платформе [см.][helm-install]

> [!IMPORTANT]
> Helm предназначен для запуска на узлах Linux. Если в кластере есть узлы Windows Server, необходимо убедиться, что стручки Helm будут запущены только на узлах Linux. Также необходимо убедиться, что все установленные диаграммы Helm также запланированы для запуска на правильных узлах. Команды в этой статье используют [селекторы узлов,][k8s-node-selector] чтобы убедиться, что стручки запланированы к правильным узлам, но не все диаграммы Helm могут подвергать селектору узлов. Вы также можете рассмотреть возможность использования других параметров в кластере, таких как [порча.][taints]

## <a name="verify-your-version-of-helm"></a>Проверить версию helm

Используйте `helm version` команду для проверки установленной версии Helm:

```console
helm version
```

Следующий пример показывает Руль версия 3.0.0 установлен:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

Для Helm v3 следуйте за шагами в [разделе Helm v3](#install-an-application-with-helm-v3). Для Helm v2 следуйте за шагами в [разделе Helm v2](#install-an-application-with-helm-v2)

## <a name="install-an-application-with-helm-v3"></a>Установите приложение с Helm v3

### <a name="add-the-official-helm-stable-charts-repository"></a>Добавить официальное хранилище стабильных графиков Helm

Используйте команду [репо руля,][helm-repo-add] чтобы добавить официальное хранилище стабильных графиков Helm.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

### <a name="find-helm-charts"></a>Поиск чартов Helm

Чарты Helm используются для развертывания приложений в кластере Kubernetes. Для поиска предварительно созданных диаграмм Helm используйте команду [поиска руля:][helm-search]

```console
helm search repo stable
```

В следующем кратком примере выходные данные содержат некоторые из доступных для использования чартов Helm:


```console
$ helm search repo stable

NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION                                       
stable/acs-engine-autoscaler            2.2.2           2.1.1                           DEPRECATED Scales worker nodes within agent pools 
stable/aerospike                        0.3.1           v4.5.0.5                        A Helm chart for Aerospike in Kubernetes          
stable/airflow                          4.10.0          1.10.4                          Airflow is a platform to programmatically autho...
stable/ambassador                       4.4.7           0.85.0                          A Helm chart for Datawire Ambassador              
stable/anchore-engine                   1.3.7           0.5.2                           Anchore container analysis and policy evaluatio...
stable/apm-server                       2.1.5           7.0.0                           The server receives data from the Elastic APM a...
stable/ark                              4.2.2           0.10.2                          DEPRECATED A Helm chart for ark                   
stable/artifactory                      7.3.1           6.1.0                           DEPRECATED Universal Repository Manager support...
stable/artifactory-ha                   0.4.1           6.2.0                           DEPRECATED Universal Repository Manager support...
stable/atlantis                         3.8.4           v0.8.2                          A Helm chart for Atlantis https://www.runatlant...
stable/auditbeat                        1.1.0           6.7.0                           A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler           0.3.3                                           Scales worker nodes within autoscaling groups.    
stable/aws-iam-authenticator            0.1.1           1.0                             A Helm chart for aws-iam-authenticator            
stable/bitcoind                         0.2.2           0.17.1                          Bitcoin is an innovative payment network and a ...
stable/bookstack                        1.1.2           0.27.4-1                        BookStack is a simple, self-hosted, easy-to-use...
stable/buildkite                        0.2.4           3                               DEPRECATED Agent for Buildkite                    
stable/burrow                           1.5.2           0.29.0                          Burrow is a permissionable smart contract machine 
stable/centrifugo                       3.1.0           2.1.0                           Centrifugo is a real-time messaging server.       
stable/cerebro                          1.3.1           0.8.5                           A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager                     v0.6.7          v0.6.2                          A Helm chart for cert-manager                     
stable/chaoskube                        3.1.2           0.14.0                          Chaoskube periodically kills random pods in you...
stable/chartmuseum                      2.4.0           0.8.2                           Host your own Helm Chart Repository               
stable/chronograf                       1.1.0           1.7.12                          Open-source web application written in Go and R...
stable/clamav                           1.0.4           1.6                             An Open-Source antivirus engine for detecting t...
stable/cloudserver                      1.0.3           8.1.5                           An open-source Node.js implementation of the Am...
stable/cluster-autoscaler               6.2.0           1.14.6                          Scales worker nodes within autoscaling groups.    
stable/cluster-overprovisioner          0.2.6           1.0                             Installs the a deployment that overprovisions t...
stable/cockroachdb                      2.1.16          19.1.5                          CockroachDB is a scalable, survivable, strongly...
stable/collabora-code                   1.0.5           4.0.3.1                         A Helm chart for Collabora Office - CODE-Edition  
stable/concourse                        8.2.7           5.6.0                           Concourse is a simple and scalable CI system.     
stable/consul                           3.9.2           1.5.3                           Highly available and distributed service discov...
stable/contour                          0.1.0           v0.15.0                         Contour Ingress controller for Kubernetes         
stable/coredns                          1.7.4           1.6.4                           CoreDNS is a DNS server that chains plugins and...
stable/cosbench                         1.0.1           0.0.6                           A benchmark tool for cloud object storage services
stable/coscale                          1.0.0           3.16.0                          CoScale Agent                                     
stable/couchbase-operator               1.0.1           1.2.1                           A Helm chart to deploy the Couchbase Autonomous...
stable/couchdb                          2.3.0           2.3.1                           DEPRECATED A database featuring seamless multi-...
stable/dask                             3.1.0           1.1.5                           Distributed computation in Python with task sch...
stable/dask-distributed                 2.0.2                                           DEPRECATED: Distributed computation in Python     
stable/datadog                          1.38.3          6.14.0                          DataDog Agent 
...
```

Чтобы обновить список чартов, используйте команду [helm repo update][helm-repo-update]. Ниже приведен пример успешного обновления репозитория:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Выполнение чартов Helm

Чтобы установить диаграммы с helm, используйте команду [установки руля][helm-install-command] и укажите имя релиза и название диаграммы для установки. Чтобы увидеть установку диаграммы Helm в действии, давайте установим базовое развертывание nginx с помощью диаграммы Helm.

```console
helm install my-nginx-ingress stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

В следующем кратком примере выходные данные содержат состояние развертывания ресурсов Kubernetes, созданное чартом Helm:

```console
$ helm install my-nginx-ingress stable/nginx-ingress \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-controller'
...
```

Используйте `kubectl get services` команду, чтобы получить *EXTERNAL-IP* вашей службы. Например, ниже команда показывает *EXTERNAL-IP* для *моего-nginx-ingress-контроллер* службы:

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-controller

NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
my-nginx-ingress-controller   LoadBalancer   10.0.123.1     <EXTERNAL-IP>   80:31301/TCP,443:31623/TCP   96s   app=nginx-ingress,component=controller,release=my-nginx-ingress
```

### <a name="list-releases"></a>Список релизов

Чтобы увидеть список релизов, установленных `helm list` в кластере, используйте команду.

```console
helm list
```

Следующий пример показывает *мой-nginx-вход* релиз развернуты в предыдущем шаге:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Очистка ресурсов

При развертывании диаграммы Helm создается несколько ресурсов Kubernetes. К ним относятся элементы pod, развертывания и службы. Чтобы очистить эти ресурсы, используйте команду [удаления руля][helm-cleanup] и укажите имя релиза, как это было в предыдущей `helm list` команде.

```console
helm uninstall my-nginx-ingress
```

Следующий пример показывает, что релиз под названием *my-nginx-ingress* был неустановлен:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="install-an-application-with-helm-v2"></a>Установите приложение с Helm v2

### <a name="create-a-service-account"></a>Создание учетной записи службы

Перед развертыванием Helm в кластере AKS с поддержкой RBAC необходимо создать учетную запись службы и привязку роли для службы Tiller. Дополнительные сведения о защите Helm и Tiller в кластере с поддержкой RBAC см. в руководстве по [использованию Tiller, пространств имен и RBAC][tiller-rbac]. Если в кластере AKS не включена поддержка RBAC, пропустите этот шаг.

Создайте файл `helm-rbac.yaml` и скопируйте в него следующий код YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Создайте учетную запись службы и привязку роли с помощью команды `kubectl apply`:

```console
kubectl apply -f helm-rbac.yaml
```

### <a name="secure-tiller-and-helm"></a>Защита Tiller и Helm

Клиент Helm и служба Tiller проверяют подлинность и взаимодействуют друг с другом с помощью протокола TLS/SSL. Этот метод проверки подлинности позволяет обеспечить безопасность кластера Kubernetes и развертываемых служб. Для повышения безопасности можно создавать собственные подписанные сертификаты. Каждый пользователь Helm получит собственный сертификат клиента, и инициализация Tiller в кластере Kubernetes будет выполняться с помощью сертификатов. Дополнительные сведения см. в статье [Using TLS/SSL between Helm and Tiller][helm2-ssl].

Благодаря кластеру Kubernetes с поддержкой RBAC вы можете контролировать уровень доступа Tiller в кластере. Можно определить пространство имен Kubernetes, в котором развертывается Tiller, и ограничить пространства имен, в которых Tiller может развертывать ресурсы. Такой подход позволяет создавать экземпляры Tiller в разных пространствах имен и определять границы развертывания, а также ограничивать область пользователей клиента Helm определенными пространствами имен. Дополнительные сведения см. в статье об [управлении доступом на основе ролей Helm][helm2-rbac].

### <a name="configure-helm"></a>Настройка Helm

Чтобы развернуть базовую службу Tiller в кластере AKS, используйте команду [helm init][helm2-init]. Если в кластере не включена поддержка RBAC, удалите значение и аргумент `--service-account`. Следующие примеры также установить [историю-максимум][helm2-history-max] до 200.

Если вы настроили TLS/SSL для Tiller и Helm, пропустите эту процедуру базовой инициализации и укажите необходимый `--tiller-tls-`, как показано в следующем примере.

```console
helm init --history-max 200 --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

Если вы настроили подключение TLS/SSL между Helm и Tiller, укажите параметры `--tiller-tls-*` и имена собственных сертификатов, как показано в следующем примере:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --history-max 200 \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os=linux"
```

### <a name="find-helm-charts"></a>Поиск чартов Helm

Чарты Helm используются для развертывания приложений в кластере Kubernetes. Для поиска предварительно созданных диаграмм Helm используйте команду [поиска руля:][helm2-search]

```console
helm search
```

В следующем кратком примере выходные данные содержат некоторые из доступных для использования чартов Helm:

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

Чтобы обновить список чартов, используйте команду [helm repo update][helm2-repo-update]. Ниже приведен пример успешного обновления репозитория:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

### <a name="run-helm-charts"></a>Выполнение чартов Helm

Чтобы установить чарты с помощью Helm, используйте команду [helm install][helm2-install-command] и укажите имя чарта для установки. Чтобы увидеть установку диаграммы Helm в действии, давайте установим базовое развертывание nginx с помощью диаграммы Helm. Если вы настроили TLS/SSL, добавьте параметр `--tls`, чтобы использовать сертификат клиента Helm.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

В следующем кратком примере выходные данные содержат состояние развертывания ресурсов Kubernetes, созданное чартом Helm:

```
$ helm install stable/nginx-ingress --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME:   flailing-alpaca
LAST DEPLOYED: Thu May 23 12:55:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                      DATA  AGE
flailing-alpaca-nginx-ingress-controller  1     0s

==> v1/Pod(related)
NAME                                                            READY  STATUS             RESTARTS  AGE
flailing-alpaca-nginx-ingress-controller-56666dfd9f-bq4cl       0/1    ContainerCreating  0         0s
flailing-alpaca-nginx-ingress-default-backend-66bc89dc44-m87bp  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                                           TYPE          CLUSTER-IP  EXTERNAL-IP  PORT(S)                     AGE
flailing-alpaca-nginx-ingress-controller       LoadBalancer  10.0.109.7  <pending>    80:31219/TCP,443:32421/TCP  0s
flailing-alpaca-nginx-ingress-default-backend  ClusterIP     10.0.44.97  <none>       80/TCP                      0s
...
```

Это займет минуту или две для *EXTERNAL-IP-адрес* службы nginx-ingress-контроллер, чтобы быть заселен и позволит вам получить к нему доступ с помощью веб-браузера.

### <a name="list-helm-releases"></a>Список выпусков Helm

Чтобы просмотреть список выпусков, установленных в кластере, используйте команду [helm list][helm2-list]. Ниже приводится следующий пример, в ходе этого года- релиз nginx-ingress, развернутый на предыдущем этапе. Если вы настроили TLS/SSL, добавьте параметр `--tls`, чтобы использовать сертификат клиента Helm.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

### <a name="clean-up-resources"></a>Очистка ресурсов

При развертывании диаграммы Helm создается несколько ресурсов Kubernetes. К ним относятся элементы pod, развертывания и службы. Для очистки этих ресурсов используйте команду `helm delete` и укажите имя выпуска, как описано в предыдущей команде `helm list`. Следующий пример удаляет релиз под названием *flailing-alpaca:*

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об управлении развертываниями приложений Kubernetes с помощью Helm см. в документации по Helm.

> [!div class="nextstepaction"]
> [Документация по Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm2-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm2-install-command]: https://v2.helm.sh/docs/helm/#helm-install
[helm2-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm2-list]: https://v2.helm.sh/docs/helm/#helm-list
[helm2-history-max]: https://v2.helm.sh/docs/using_helm/#initialize-helm-and-install-tiller
[helm2-rbac]: https://v2.helm.sh/docs/using_helm/#role-based-access-control
[helm2-repo-update]: https://v2.helm.sh/docs/helm/#helm-repo-update
[helm2-search]: https://v2.helm.sh/docs/helm/#helm-search
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
[helm2-ssl]: https://v2.helm.sh/docs/using_helm/#using-ssl-between-helm-and-tiller
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md
