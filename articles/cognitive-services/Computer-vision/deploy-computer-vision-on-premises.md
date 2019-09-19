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
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 7560f2395447e81dcd01e1d3e092b39b129b4ce3
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129822"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Использование контейнера Компьютерное зрение с Kubernetes и Helm

Одним из вариантов управления контейнерами Компьютерное зрение в локальной среде является использование Kubernetes и Helm. Используя Kubernetes и Helm для определения Компьютерное зрение образа контейнера, мы создадим пакет Kubernetes. Этот пакет будет развернут в кластере Kubernetes в локальной среде. Наконец, мы продемонстрируем, как тестировать развернутые службы. Дополнительные сведения о выполнении контейнеров DOCKER без оркестрации Kubernetes см. в [статье Установка и запуск компьютерное зрение контейнерах](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Предварительные требования

Перед использованием Компьютерное зрение контейнеров в локальной среде выполните следующие предварительные требования:

|Обязательное значение|Цель|
|--|--|
| Учетная запись Azure | Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure][free-azure-account], прежде чем начинать работу. |
| Доступ к реестру контейнеров | Чтобы Kubernetes мог извлечь образы DOCKER в кластер, ему потребуется доступ к реестру контейнеров. Сначала требуется [запросить доступ к реестру контейнеров][vision-preview-access] . |
| Kubernetes CLI | Интерфейс [командной строки Kubernetes][kubernetes-cli] требуется для управления общими учетными данными из реестра контейнеров. Kubernetes также требуется перед Helm, который является диспетчером пакетов Kubernetes. |
| Helm CLI | В рамках установки [Helm CLI][helm-install] необходимо также инициализировать Helm, который [установит.][tiller-install] |
| Ресурс Компьютерное зрение |Для использования контейнера необходимо следующее:<br><br>Ресурс Azure **компьютерное зрение** и соответствующий ключ API для конечной точки. Оба значения доступны на страницах обзора и ключей для ресурса и необходимы для запуска контейнера.<br><br>**{API_KEY}** : Один из двух доступных ключей ресурсов на странице " **ключи** "<br><br>**{ENDPOINT_URI}** : Конечная точка, указанная на странице **обзора**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Подключение к кластеру Kubernetes

Предполагается, что на главном компьютере есть доступный кластер Kubernetes. Ознакомьтесь с руководством по [развертыванию кластера Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) для концептуального понимания того, как развернуть кластер Kubernetes на главном компьютере.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Предоставление учетных данных DOCKER в кластере Kubernetes

Чтобы разрешить кластеру `docker pull` Kubernetes настроенные образы `containerpreview.azurecr.io` из реестра контейнеров, необходимо переместить учетные данные DOCKER в кластер. Выполните приведенную ниже [команду,чтобысоздатьсекретDOCKER-Registryнаосновеучетныхданных,предоставленныхвпредварительныхтребованияхдлядоступакреестру`kubectl create`][kubectl-create] контейнеров.

В выбранном интерфейсе командной строки выполните следующую команду. Обязательно замените `<username>`, `<password>`и `<email-address>` на учетные данные реестра контейнеров.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Если у вас уже есть доступ `containerpreview.azurecr.io` к реестру контейнеров, можно создать секрет Kubernetes, используя вместо него общий флаг. Рассмотрим следующую команду, которая выполняется для JSON конфигурации DOCKER.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

После успешного создания секрета на консоли выводятся следующие выходные данные.

```console
secret "containerpreview" created
```

Чтобы убедиться, что секрет создан, выполните [`kubectl get`][kubectl-get] `secrets` с флагом.

```console
kuberctl get secrets
```

При исполнении `kubectl get secrets` выводятся все настроенные секреты.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Настройка значений диаграммы Helm для развертывания

# <a name="readtabread"></a>[чтение](#tab/read)

Сначала создайте папку с именем *Read*, а затем вставьте следующее содержимое YAML в новый файл с именем *Chart. yml*.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

Чтобы настроить значения по умолчанию для диаграммы Helm, скопируйте и вставьте следующий YAML в файл с `values.yaml`именем. Замените комментарии `# {API_KEY}`исобственнымизначениями. `# {ENDPOINT_URI}`

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
> Если значения `apikey` и не указаны, срок действия служб истечет через 15 минут. `billing` Аналогичным образом проверка завершится ошибкой, так как службы будут недоступны.

Создайте папку *Templates* в каталоге *Read* . Скопируйте и вставьте следующий YAML в файл с именем `deployment.yaml`. `deployment.yaml` Файл будет служить шаблоном Helm.

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

# <a name="recognize-texttabrecognize-text"></a>[Распознавание текста](#tab/recognize-text)

Начните с создания папки с именем *распознавателя текста*, скопируйте и вставьте следующее содержимое YAML в новый файл с именем `Chart.yml`.

```yaml
apiVersion: v1
name: text-recognizer
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-recognize-text to a Kubernetes cluster
```

Чтобы настроить значения по умолчанию для диаграммы Helm, скопируйте и вставьте следующий YAML в файл с `values.yaml`именем. Замените комментарии `# {API_KEY}`исобственнымизначениями. `# {ENDPOINT_URI}`

```yaml
# These settings are deployment specific and users can provide customizations

recognizeText:
  enabled: true
  image:
    name: cognitive-services-recognize-text
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-recognize-text
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Если значения `apikey` и не указаны, срок действия служб истечет через 15 минут. `billing` Аналогичным образом проверка завершится ошибкой, так как службы будут недоступны.

Создайте папку *Templates* в каталоге *распознавателя текста* . Скопируйте и вставьте следующий YAML в файл с именем `deployment.yaml`. `deployment.yaml` Файл будет служить шаблоном Helm.

> Шаблоны создают файлы манифеста, YAML описания ресурсов, которые Kubernetes могут понять. [-Helm шаблон диаграммы][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: text-recognizer
spec:
  template:
    metadata:
      labels:
        app: text-recognizer-app
    spec:
      containers:
      - name: {{.Values.recognizeText.image.name}}
        image: {{.Values.recognizeText.image.registry}}{{.Values.recognizeText.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.recognizeText.image.args.eula}}
        - name: billing
          value: {{.Values.recognizeText.image.args.billing}}
        - name: apikey
          value: {{.Values.recognizeText.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.recognizeText.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: text-recognizer
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: text-recognizer-app
```

Шаблон указывает службу подсистемы балансировки нагрузки и развертывание контейнера или образа для распознавания текста.

***

### <a name="the-kubernetes-package-helm-chart"></a>Пакет Kubernetes (диаграмма Helm)

*Диаграмма Helm* содержит конфигурацию образов DOCKER, которые необходимо извлечь из `containerpreview.azurecr.io` реестра контейнеров.

> [Helm диаграмма][helm-charts] — это коллекция файлов, описывающих связанный набор ресурсов Kubernetes. Отдельную диаграмму можно использовать для развертывания чего-либо простого, такого как memcached Pod или что-то сложного, подобно полному стеку веб-приложений с серверами HTTP, базами данных, кэшами и т. д.

Предоставленные *диаграммы Helm* извлекают образы DOCKER службы компьютерное зрение и соответствующую службу из `containerpreview.azurecr.io` реестра контейнеров.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Установка диаграммы Helm в кластере Kubernetes

# <a name="readtabread"></a>[чтение](#tab/read)

Чтобы установить *диаграмму Helm*, необходимо выполнить [`helm install`][helm-install-cmd] команду. Обязательно выполните команду Install из каталога, расположенного над `read` папкой.

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
λ kubectl get all
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

# <a name="recognize-texttabrecognize-text"></a>[Распознавание текста](#tab/recognize-text)

Чтобы установить *диаграмму Helm*, необходимо выполнить [`helm install`][helm-install-cmd] команду. Обязательно выполните команду Install из каталога, расположенного над `text-recognizer` папкой.

```console
helm install text-recognizer --name text-recognizer
```

Ниже приведен пример выходных данных, которые можно ожидать при успешном выполнении установки.

```console
NAME:   text-recognizer
LAST DEPLOYED: Thu Aug 22 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                              READY  STATUS             RESTARTS  AGE
text-recognizer-57cb76bcf7-45sdh  0/1    ContainerCreating  0         0s

==> v1/Service
NAME             TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
text-recognizer  LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME             READY  UP-TO-DATE  AVAILABLE  AGE
text-recognizer  0/1    1           0          0s
```

Для завершения развертывания Kubernetes может потребоваться несколько минут. Чтобы убедиться в правильности развертывания и доступности модулей Pod и служб, выполните следующую команду:

```console
kubectl get all
```

Должны отобразиться примерно такие результаты:

```console
λ kubectl get all
NAME                                   READY   STATUS    RESTARTS   AGE
pod/text-recognizer-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes        ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/text-recognizer   LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                              READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/text-recognizer   1/1     1            1           17s

NAME                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/text-recognizer-57cb76bcf7   1         1         1       17s
```

***

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Следующие шаги

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
[helm-install-cmd]: https://helm.sh/docs/helm/#helm-install
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/developing_charts
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-preview-access]: computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
