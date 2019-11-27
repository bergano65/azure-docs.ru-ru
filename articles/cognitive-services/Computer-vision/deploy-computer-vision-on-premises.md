---
title: Использование контейнера Компьютерное зрение с Kubernetes и Helm
titleSuffix: Azure Cognitive Services
description: Разверните контейнер Компьютерное зрение в экземпляре контейнера Azure и протестируйте его в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 22ec16f66c463cde49adbc9c472e461169df5eeb
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383782"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Использование контейнера Компьютерное зрение с Kubernetes и Helm

Одним из вариантов управления контейнерами Компьютерное зрение в локальной среде является использование Kubernetes и Helm. Используя Kubernetes и Helm для определения Компьютерное зрение образа контейнера, мы создадим пакет Kubernetes. Этот пакет будет развернут в кластере Kubernetes в локальной среде. Наконец, мы продемонстрируем, как тестировать развернутые службы. Дополнительные сведения о выполнении контейнеров DOCKER без оркестрации Kubernetes см. в [статье Установка и запуск компьютерное зрение контейнерах](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>предварительным требованиям

Перед использованием Компьютерное зрение контейнеров в локальной среде выполните следующие предварительные требования:

|обязательные|Назначение|
|--|--|
| Учетная запись Azure | Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure][free-azure-account] , прежде чем начинать работу. |
| Kubernetes CLI | Интерфейс [командной строки Kubernetes][kubernetes-cli] требуется для управления общими учетными данными из реестра контейнеров. Kubernetes также требуется перед Helm, который является диспетчером пакетов Kubernetes. |
| Helm CLI | В рамках установки [Helm CLI][helm-install] необходимо также инициализировать Helm, который [установит.][tiller-install] |
| Ресурс Компьютерное зрение |Для использования контейнера необходимо следующее:<br><br>Ресурс Azure **компьютерное зрение** и соответствующий ключ API для конечной точки. Оба значения доступны на страницах обзора и ключей для ресурса и необходимы для запуска контейнера.<br><br>**{API_KEY}** : один из двух доступных ключей ресурсов на странице " **ключи** "<br><br>**{ENDPOINT_URI}** : конечная точка, указанная на странице **обзора**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Подключение к кластеру Kubernetes

Предполагается, что на главном компьютере есть доступный кластер Kubernetes. Ознакомьтесь с руководством по [развертыванию кластера Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) для концептуального понимания того, как развернуть кластер Kubernetes на главном компьютере.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Предоставление учетных данных DOCKER в кластере Kubernetes

Чтобы разрешить кластеру Kubernetes `docker pull` настроенные образы из реестра `containerpreview.azurecr.io` контейнеров, необходимо переносить учетные данные DOCKER в кластер. Выполните приведенную ниже команду [`kubectl create`][kubectl-create] , чтобы создать *секрет DOCKER-Registry* на основе учетных данных, указанных в предварительных требованиях для доступа к реестру контейнеров.

В выбранном интерфейсе командной строки выполните следующую команду. Не забудьте заменить `<username>`, `<password>`и `<email-address>` учетными данными реестра контейнеров.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Если у вас уже есть доступ к реестру контейнеров `containerpreview.azurecr.io`, можно создать секрет Kubernetes, используя вместо него общий флаг. Рассмотрим следующую команду, которая выполняется для JSON конфигурации DOCKER.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

После успешного создания секрета на консоли выводятся следующие выходные данные.

```console
secret "containerpreview" created
```

Чтобы убедиться, что секрет создан, выполните [`kubectl get`][kubectl-get] с флагом `secrets`.

```console
kubectl get secrets
```

При запуске `kubectl get secrets` выводятся все настроенные секреты.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Настройка значений диаграммы Helm для развертывания

Сначала создайте папку с именем *Read*, а затем вставьте следующее содержимое YAML в новый файл с именем *Chart. yml*.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

Чтобы настроить значения по умолчанию для диаграммы Helm, скопируйте и вставьте следующий YAML в файл с именем `values.yaml`. Замените `# {ENDPOINT_URI}` и `# {API_KEY}` комментарии собственными значениями.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Если значения `billing` и `apikey` не указаны, срок действия этих служб истечет через 15 минут. Аналогичным образом проверка завершится ошибкой, так как службы будут недоступны.

Создайте папку *Templates* в каталоге *Read* . Скопируйте и вставьте следующий YAML в файл с именем `deployment.yaml`. Файл `deployment.yaml` будет служить шаблоном Helm.

> Шаблоны создают файлы манифеста, YAML описания ресурсов, которые Kubernetes могут понять. [-Helm шаблон диаграммы][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: read
spec:
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Шаблон указывает службу подсистемы балансировки нагрузки и развертывание контейнера или образа для чтения.

### <a name="the-kubernetes-package-helm-chart"></a>Пакет Kubernetes (диаграмма Helm)

*Диаграмма Helm* содержит конфигурацию, из которой образы DOCKER нужно извлечь из реестра контейнеров `containerpreview.azurecr.io`.

> [Helm диаграмма][helm-charts] — это коллекция файлов, описывающих связанный набор ресурсов Kubernetes. Отдельную диаграмму можно использовать для развертывания чего-либо простого, такого как memcached Pod или что-то сложного, подобно полному стеку веб-приложений с серверами HTTP, базами данных, кэшами и т. д.

Предоставленные *диаграммы Helm* извлекают образы docker службы компьютерное зрение и соответствующую службу из реестра контейнеров `containerpreview.azurecr.io`.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Установка диаграммы Helm в кластере Kubernetes

Чтобы установить *диаграмму Helm*, необходимо выполнить команду [`helm install`][helm-install-cmd] . Обязательно выполните команду Install из каталога, расположенного над папкой `read`.

```console
helm install read --name read
```

Ниже приведен пример выходных данных, которые можно ожидать при успешном выполнении установки.

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

Для завершения развертывания Kubernetes может потребоваться несколько минут. Чтобы убедиться в правильности развертывания и доступности модулей Pod и служб, выполните следующую команду:

```console
kubectl get all
```

Должны отобразиться примерно такие результаты:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об установке приложений с помощью Helm в службе Kubernetes Azure (AKS) см. [здесь][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Контейнеры Cognitive Services][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
