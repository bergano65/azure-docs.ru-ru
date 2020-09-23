---
title: Использование контейнера Компьютерное зрение с Kubernetes и Helm
titleSuffix: Azure Cognitive Services
description: Узнайте, как развернуть контейнер Компьютерное зрение с помощью Kubernetes и Helm.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 9a8e0dde8b24c39180a584c26af725ab82ea0176
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907104"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Использование контейнера Компьютерное зрение с Kubernetes и Helm

Одним из вариантов управления контейнерами Компьютерное зрение в локальной среде является использование Kubernetes и Helm. Используя Kubernetes и Helm для определения Компьютерное зрение образа контейнера, мы создадим пакет Kubernetes. Этот пакет будет развернут в кластере Kubernetes в локальной среде. Наконец, мы продемонстрируем, как тестировать развернутые службы. Дополнительные сведения о выполнении контейнеров DOCKER без оркестрации Kubernetes см. в [статье Установка и запуск компьютерное зрение контейнерах](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Предварительные требования

Перед использованием Компьютерное зрение контейнеров в локальной среде выполните следующие предварительные требования:

| Обязательно | Назначение |
|----------|---------|
| Учетная запись Azure | Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись][free-azure-account], прежде чем начинать работу. |
| Kubernetes CLI | Интерфейс [командной строки Kubernetes][kubernetes-cli] требуется для управления общими учетными данными из реестра контейнеров. Kubernetes также требуется перед Helm, который является диспетчером пакетов Kubernetes. |
| Интерфейс командной строки Helm | Установите интерфейс [командной строки Helm][helm-install], который используется для установки диаграммы Helm (определение пакета контейнера). |
| Ресурс Компьютерное зрение |Для использования контейнера необходимо следующее:<br><br>Ресурс Azure **компьютерное зрение** и соответствующий ключ API для конечной точки. Оба значения доступны на страницах обзора и ключей для ресурса и необходимы для запуска контейнера.<br><br>**{API_KEY}**: один из двух доступных ключей ресурсов на странице " **ключи** "<br><br>**{ENDPOINT_URI}**: конечная точка, указанная на странице **обзора**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Подключение к кластеру Kubernetes

Предполагается, что на главном компьютере есть доступный кластер Kubernetes. Ознакомьтесь с руководством по [развертыванию кластера Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) для концептуального понимания того, как развернуть кластер Kubernetes на главном компьютере. Дополнительные сведения о развертываниях можно найти в [документации по Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Предоставление учетных данных DOCKER в кластере Kubernetes

Чтобы разрешить кластеру Kubernetes `docker pull` настроенные образы из `containerpreview.azurecr.io` реестра контейнеров, необходимо переместить учетные данные DOCKER в кластер. Выполните [`kubectl create`][kubectl-create] приведенную ниже команду, чтобы создать *секрет DOCKER-Registry* на основе учетных данных, предоставленных в предварительных требованиях для доступа к реестру контейнеров.

В выбранном интерфейсе командной строки выполните следующую команду. Обязательно замените `<username>` , `<password>` и на `<email-address>` учетные данные реестра контейнеров.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Если у вас уже есть доступ к `containerpreview.azurecr.io` реестру контейнеров, можно создать секрет Kubernetes, используя вместо него общий флаг. Рассмотрим следующую команду, которая выполняется для JSON конфигурации DOCKER.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

После успешного создания секрета на консоли выводятся следующие выходные данные.

```console
secret "containerpreview" created
```

Чтобы убедиться, что секрет создан, выполните [`kubectl get`][kubectl-get] с `secrets` флагом.

```console
kubectl get secrets
```

При исполнении `kubectl get secrets` выводятся все настроенные секреты.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Настройка значений диаграммы Helm для развертывания

Начните с создания папки с именем *Read*. Затем вставьте следующее содержимое YAML в новый файл с именем `chart.yaml` :

```yaml
apiVersion: v2
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
dependencies:
- name: rabbitmq
  condition: read.image.args.rabbitmq.enabled
  version: ^6.12.0
  repository: https://kubernetes-charts.storage.googleapis.com/
- name: redis
  condition: read.image.args.redis.enabled
  version: ^6.0.0
  repository: https://kubernetes-charts.storage.googleapis.com/
```

Чтобы настроить значения по умолчанию для диаграммы Helm, скопируйте и вставьте следующий YAML в файл с именем `values.yaml` . Замените `# {ENDPOINT_URI}` комментарии и `# {API_KEY}` собственными значениями. При необходимости настройте Ресултекспиратионпериод, Redis и RabbitMQ.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry:  containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
      
      # Result expiration period setting. Specify when the system should clean up recognition results.
      # For example, resultExpirationPeriod=1, the system will clear the recognition result 1hr after the process.
      # resultExpirationPeriod=0, the system will clear the recognition result after result retrieval.
      resultExpirationPeriod: 1
      
      # Redis storage, if configured, will be used by read container to store result records.
      # A cache is required if multiple read containers are placed behind load balancer.
      redis:
        enabled: false # {true/false}
        password: password

      # RabbitMQ is used for dispatching tasks. This can be useful when multiple read containers are
      # placed behind load balancer.
      rabbitmq:
        enabled: false # {true/false}
        rabbitmq:
          username: user
          password: password
```

> [!IMPORTANT]
> - Если `billing` значения и `apikey` не указаны, срок действия служб истекает через 15 минут. Аналогичным образом проверка завершается сбоем, так как службы недоступны.
> 
> - При развертывании нескольких контейнеров чтения за подсистемой балансировки нагрузки, например в разделе Docker Compose или Kubernetes, необходим внешний кэш. Так как контейнер обработки и контейнер запроса GET могут отличаться, внешний кэш сохраняет результаты и разделяет их между контейнерами. Дополнительные сведения о параметрах кэша см. в разделе [Configure компьютерное зрение Container DOCKER](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-resource-container-config).
>

Создайте папку *Templates* в каталоге *Read* . Скопируйте и вставьте следующий YAML в файл с именем `deployment.yaml` . `deployment.yaml`Файл будет служить шаблоном Helm.

> Шаблоны создают файлы манифеста, YAML описания ресурсов, которые Kubernetes могут понять. [-Helm шаблон диаграммы][chart-template-guide]

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
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
        args:        
        - ReadEngineConfig:ResultExpirationPeriod={{ .Values.read.image.args.resultExpirationPeriod }}
        {{- if .Values.read.image.args.rabbitmq.enabled }}
        - Queue:RabbitMQ:HostName={{ include "rabbitmq.hostname" . }}
        - Queue:RabbitMQ:Username={{ .Values.read.image.args.rabbitmq.rabbitmq.username }}
        - Queue:RabbitMQ:Password={{ .Values.read.image.args.rabbitmq.rabbitmq.password }}
        {{- end }}      
        {{- if .Values.read.image.args.redis.enabled }}
        - Cache:Redis:Configuration={{ include "redis.connStr" . }}
        {{- end }}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}      
--- 
apiVersion: v1
kind: Service
metadata:
  name: read-service
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

В той же папке *Templates* скопируйте и вставьте следующие вспомогательные функции в `helpers.tpl` . `helpers.tpl` определяет полезные функции, помогающие создать шаблон Helm.

```yaml
{{- define "rabbitmq.hostname" -}}
{{- printf "%s-rabbitmq" .Release.Name -}}
{{- end -}}

{{- define "redis.connStr" -}}
{{- $hostMaster := printf "%s-redis-master:6379" .Release.Name }}
{{- $hostSlave := printf "%s-redis-slave:6379" .Release.Name -}}
{{- $passWord := printf "password=%s" .Values.read.image.args.redis.password -}}
{{- $connTail := "ssl=False,abortConnect=False" -}}
{{- printf "%s,%s,%s,%s" $hostMaster $hostSlave $passWord $connTail -}}
{{- end -}}
```
Шаблон указывает службу подсистемы балансировки нагрузки и развертывание контейнера или образа для чтения.

### <a name="the-kubernetes-package-helm-chart"></a>Пакет Kubernetes (диаграмма Helm)

*Диаграмма Helm* содержит конфигурацию образов DOCKER, которые необходимо извлечь из `containerpreview.azurecr.io` реестра контейнеров.

> [Helm диаграмма][helm-charts] — это коллекция файлов, описывающих связанный набор ресурсов Kubernetes. Отдельную диаграмму можно использовать для развертывания чего-либо простого, такого как memcached Pod или что-то сложного, подобно полному стеку веб-приложений с серверами HTTP, базами данных, кэшами и т. д.

Предоставленные *диаграммы Helm* извлекают образы docker службы компьютерное зрение и соответствующую службу из `containerpreview.azurecr.io` реестра контейнеров.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Установка диаграммы Helm в кластере Kubernetes

Чтобы установить *диаграмму Helm*, необходимо выполнить [`helm install`][helm-install-cmd] команду. Обязательно выполните команду Install из каталога, расположенного над `read` папкой.

```console
helm install read ./read
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

## <a name="next-steps"></a>Дальнейшие действия

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
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
