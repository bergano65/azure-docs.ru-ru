---
title: Используйте контейнер Computer Vision с Kubernetes и Helm
titleSuffix: Azure Cognitive Services
description: Развернуть контейнер Computer Vision в azure Container Instance и протестировать его в веб-браузере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 126060875c09d70b8680447d78b7cf6ccdd782af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458024"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Используйте контейнер Computer Vision с Kubernetes и Helm

Одним из вариантов управления контейнерами Computer Vision на месте является использование Kubernetes и Helm. Используя Kubernetes и Helm для определения изображения контейнера Computer Vision, мы создадим пакет Kubernetes. Этот пакет будет развернут в кластере Kubernetes на месте. Наконец, мы рассмотрим, как протестировать развернутые службы. Для получения дополнительной информации о запуске контейнеров Docker без согласования Kubernetes [см.](computer-vision-how-to-install-containers.md)

## <a name="prerequisites"></a>Предварительные требования

Следующие предпосылки перед использованием контейнеров Computer Vision на месте:

| Обязательно | Назначение |
|----------|---------|
| Учетная запись Azure | Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись][free-azure-account], прежде чем начинать работу. |
| Кубернетес CLI | [Kubernetes CLI][kubernetes-cli] необходим для управления общими учетных данными из реестра контейнеров. Kubernetes также необходимо перед Helm, который является менеджером пакетов Kubernetes. |
| Интерфейс командной строки Helm | Установите [Helm CLI][helm-install], который используется для установки диаграммы руля (определение контейнерного пакета). |
| Ресурс компьютерного зрения |Для использования контейнера необходимо следующее:<br><br>Ресурс Azure **Computer Vision** и связанный с ним API ключ к конечным точкам URI. Оба значения доступны на страницах «Обзор» и «Ключи» для ресурса и необходимы для запуска контейнера.<br><br>**«API_KEY:** Один из двух доступных ключей ресурса на странице **Ключей**<br><br>**(ENDPOINT_URI:** конечная точка, представленная на странице **Обзор**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Подключение к кластеру Kubernetes

Ожидается, что компьютер-хозяин будет иметь доступный кластер Kubernetes. Ознакомьтесь с этим учебником по [развертыванию кластера Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) для получения концептуального понимания того, как развернуть кластер Kubernetes на компьютер-хост.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Обмен учетными данными Docker с кластером Kubernetes

Чтобы позволить кластеру `docker pull` Kubernetes настроить изображение (ы) из реестра `containerpreview.azurecr.io` контейнеров, необходимо перевести учетные данные докера в кластер. Выполните [`kubectl create`][kubectl-create] команду ниже, чтобы создать *секрет докер-реестра* на основе учетных данных, предоставленных из контейнерного реестра доступа предпосылки.

Из выбора интерфейса командной строки запустите следующую команду. Не забудьте `<username>`заменить `<password>` `<email-address>` , , и с реестром контейнера полномочия.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Если у вас уже `containerpreview.azurecr.io` есть доступ к реестру контейнеров, можно создать секрет Kubernetes, используя вместо этого общий флаг. Рассмотрим следующую команду, которая выполняется против конфигурации Docker JSON.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Следующий выход печатается на консоли, когда секрет был успешно создан.

```console
secret "containerpreview" created
```

Чтобы убедиться, что секрет был [`kubectl get`][kubectl-get] создан, выполнить с флагом. `secrets`

```console
kubectl get secrets
```

Выполнение `kubectl get secrets` отпечатков все настроенные секреты.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Настройка значений диаграммы румов для развертывания

Начните с создания папки с именем *читать*, а затем вставить следующее содержание YAML в новый файл под названием *Chart.yml*.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

Чтобы настроить значения по умолчанию диаграммы Helm, скопируйте и `values.yaml`вставьте следующие YAML в файл с именем. Замените `# {ENDPOINT_URI}` `# {API_KEY}` и комментарии с вашими собственными значениями.

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
> Если `billing` стоимость `apikey` и значения не предоставляются, срок действия услуг истекает через 15 минут. Аналогичным образом, проверка не будет, так как службы не будут доступны.

Создайте папку *шаблонов* под каталогом *чтения.* Копировать и вставить следующие YAML `deployment.yaml`в файл с именем . Файл `deployment.yaml` будет служить шаблоном Helm.

> Шаблоны генерируют файлы, которые представляют собой описания ресурсов, форматированных YAML, которые могут понять Kubernetes. [- Шлем Диаграмма Шаблон Руководство][chart-template-guide]

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

Шаблон определяет службу балансоостатков нагрузки и развертывание контейнера/изображения для чтения.

### <a name="the-kubernetes-package-helm-chart"></a>Пакет Kubernetes (Хелм-чарт)

*Диаграмма Helm* содержит конфигурацию, изображение которого докера (ы) вытащить из `containerpreview.azurecr.io` реестра контейнеров.

> [Диаграмма Helm][helm-charts] представляет собой набор файлов, описывающие связанный набор ресурсов Kubernetes. Единая диаграмма может быть использована для развертывания чего-то простого, например, мемкачанных стручок, или что-то сложное, например, полный стек веб-приложений с серверами HTTP, базами данных, кэшами и так далее.

Предоставленные *диаграммы Helm* вытягивают изображения докера службы компьютерного зрения и соответствующую услугу из `containerpreview.azurecr.io` реестра контейнеров.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Установка диаграммы Хелма в кластере Кубернете

Для установки *диаграммы руля*нам [`helm install`][helm-install-cmd] нужно выполнить команду. Убедитесь в выполнении команды установки `read` из каталога над папкой.

```console
helm install read ./read
```

Вот пример вывода, который можно ожидать от успешного выполнения установки:

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

Развертывание Kubernetes может занять несколько минут. Чтобы подтвердить, что как стручки, так и службы должным образом развернуты и доступны, выполните следующую команду:

```console
kubectl get all
```

Вы должны ожидать увидеть что-то похожее на следующий вывод:

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

Для получения более подробной информации об установке приложений с шлемом в Azure Kubernetes Service (AKS), [посетите здесь][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Контейнеры когнитивных услуг][cog-svcs-containers]

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
