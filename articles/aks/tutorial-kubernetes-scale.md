---
title: Руководство по Kubernetes в Azure. Масштабирование приложения
description: В этом руководстве по Службе Azure Kubernetes (AKS) вы узнаете, как выполнить масштабирование узлов и модулей pod в Kubernetes и реализовать горизонтальное автомасштабирование модулей pod.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: twhitney
ms.custom: mvc
ms.openlocfilehash: 062e16c0d196cf91d6e0adde46ed973f1c0d1191
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304429"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>Руководство по Масштабирование приложений в Службе Azure Kubernetes (AKS)

Если вы выполнили инструкции в руководствах, то у вас имеется работающий кластер Kubernetes в AKS, и вы развернули в нем пример приложения для голосования Azure. В этом руководстве (часть 5 из 7) описывается масштабирование pod, содержащихся в приложении, и их автомасштабирование. Вы также узнаете, как масштабировать количество узлов виртуальной машины Azure, чтобы менять емкость кластера для размещения рабочих нагрузок. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Масштабирование узлов Kubernetes.
> * Масштабирование модулей pod Kubernetes вручную для выполнения приложения.
> * Настройка модулей pod для автоматического масштабирования, которые запускают внешний интерфейс приложения.

В дополнительных руководствах описано, как обновить приложение Azure для голосования до новой версии.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение было упаковано в образ контейнера. Этот образ был передан в Реестр контейнеров Azure, и вы создали кластер AKS. Затем приложение было развернуто в кластере AKS. Если вы не выполнили эти действия, вы можете начать с раздела руководства 1 [Создание образов контейнеров][aks-tutorial-prepare-app].

Для выполнения задач из этого руководства требуется Azure CLI 2.0.53 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="manually-scale-pods"></a>Масштабирование pod вручную

В рамках предыдущих руководств были развернуты внешний интерфейс приложения Azure для голосования и экземпляр Redis, а также создана одна реплика. Чтобы просмотреть число и состояние модулей pod в кластере, используйте команду [kubectl get][kubectl-get] следующим образом:

```console
kubectl get pods
```

В следующем примере выходных данных показано по одному интерфейсному и серверному модулю pod:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Чтобы вручную изменить количество модулей pod в развертывании *azure-vote-front*, используйте команду [kubectl scale][kubectl-scale]. В следующем примере увеличивает число интерфейсных модулей pod до *5*.

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Выполните команду [kubectl get pods][kubectl-get] еще раз, чтобы убедиться, что AKS создает дополнительные модули pod. Они становятся доступными примерно через минуту.

```console
$ kubectl get pods

                                    READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Автомасштабирование pod

Kubernetes поддерживает [горизонтальное автомасштабирование pod][kubernetes-hpa] для изменения числа групп pod в развертывании в зависимости от использования ЦП или других выбранных метрик. [Сервер метрик][metrics-server] используется для предоставления сведений об использовании ресурсов в Kubernetes. Он автоматически развертывается в кластерах AKS версии 1.10 и выше. Чтобы узнать версию кластера AKS, используйте команду [az aks show][az-aks-show], как показано в следующем примере:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion
```

Если версия кластера AKS ниже *1.10*, установите сервер метрик. В противном случае пропустите этот шаг. Чтобы установить его, клонируйте репозиторий GitHub `metrics-server` и установите примеры определений ресурсов. Чтобы просмотреть содержимое этих определений YAML, см. руководство по [использованию сервера метрик для Kuberenetes 1.8+][metrics-server-github].

```console
git clone https://github.com/kubernetes-incubator/metrics-server.git
kubectl create -f metrics-server/deploy/1.8+/
```

Чтобы использовать инструмент автомасштабирования, для всех контейнеров в группах pod и всех групп pod необходимо определить запросы и лимиты ресурсов ЦП. В развертывании `azure-vote-front` контейнер внешнего приложения уже запрашивает 0,25 ресурсов ЦП с лимитом в 0,5 ресурсов ЦП. Эти запросы и ограничения ресурсов определены, как показано в следующем фрагменте кода:

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

В следующем примере используется команда [kubectl autoscale][kubectl-autoscale] для автомасштабирования числа модулей pod в развертывании *azure-vote-front*. Если загрузка ЦП превышает 50 %, то инструмент автомасштабирования увеличивает число модулей pod максимум до *10* экземпляров. Затем для развертывания определяются как минимум *3* экземпляра:

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Чтобы просмотреть состояние инструмента автомасштабирования, используйте команду `kubectl get hpa` следующим образом:

```
$ kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Через несколько минут при минимальной нагрузке на приложение Azure для голосования число реплик модуля pod автоматически уменьшится до 3. Вы можете использовать команду `kubectl get pods` еще раз, чтобы увидеть ненужные модули pod, которые были удалены.

## <a name="manually-scale-aks-nodes"></a>Масштабирование узлов AKS вручную

Если вы создали кластер Kubernetes с помощью команд в предыдущем руководстве, то у него один узел. Если вы планируете увеличение или уменьшение рабочих нагрузок контейнеров в кластере, то можете соответствующим образом изменить число узлов вручную.

В следующем примере в кластере Kubernetes *myAKSCluster* число узлов увеличивается до трех. Для выполнения этой команды требуется несколько минут.

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

Если кластер успешно масштабирован, выходные данные будут соответствовать приведенным ниже:

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы использовали различные возможности масштабирования кластера Kubernetes. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Масштабирование модулей pod Kubernetes вручную для выполнения приложения.
> * Настройка модулей pod для автоматического масштабирования, которые запускают внешний интерфейс приложения.
> * Масштабирование узлов Kubernetes вручную.

Перейдите к следующему руководству, чтобы узнать, как обновить приложение в Kubernetes.

> [!div class="nextstepaction"]
> [Обновление приложения в Kubernetes][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-incubator/metrics-server/tree/master/deploy/1.8%2B
[metrics-server]: https://v1-12.docs.kubernetes.io/docs/tasks/debug-application-cluster/core-metrics-pipeline/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
