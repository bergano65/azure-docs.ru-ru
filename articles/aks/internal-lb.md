---
title: Создание внутренней подсистемы балансировки нагрузки в Службе Azure Kubernetes (AKS)
description: Узнайте, как создать и использовать внутреннюю подсистему балансировки нагрузки для предоставления доступа к службам через AKS (Службу Azure Kubernetes).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 5842003d43d4268d0f663e8a57e40562a480e252
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "67615150"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Использование внутренней подсистемы балансировки нагрузки со Службой Azure Kubernetes (AKS)

Чтобы ограничить доступ к приложениям в Службе Azure Kubernetes (AKS), можно создать и использовать внутреннюю подсистему балансировки нагрузки. Внутренняя подсистема балансировки нагрузки позволяет сделать службу Kubernetes доступной только для приложений, работающих в той же виртуальной сети, что и кластер Kubernetes. В этой статье описаны процессы создания и использования внутренней подсистемы балансировки нагрузки со Службой Azure Kubernetes (AKS).

> [!NOTE]
> Доступны два номера SKU Azure Load Balancer: ценовых категорий *Базовый* и *Стандартный*. По умолчанию используется *базовый* номер SKU, если манифест службы используется для создания балансировщика нагрузки в AKS. Дополнительные сведения см. в статье [Сравнение SKU в подсистеме балансировки нагрузки Azure][azure-lb-comparison].

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, ознакомьтесь с кратким руководством по AKS, [используя Azure CLI][aks-quickstart-cli] или [с помощью портал Azure][aks-quickstart-portal].

Также требуется Azure CLI версии 2.0.59 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если необходимо установить или обновить, см. раздел [install Azure CLI][install-azure-cli].

Субъекту-службе кластера AKS требуется разрешение для управления сетевыми ресурсами, если используется существующая подсеть или группа ресурсов. В общем случае назначьте роль *участника сети* субъекту-службе для делегированных ресурсов. Дополнительные сведения о разрешениях см. в статье [Делегирование AKS доступ к другим ресурсам Azure][aks-sp].

## <a name="create-an-internal-load-balancer"></a>Создание внутреннего балансировщика нагрузки

Чтобы создать внутреннюю подсистему балансировки нагрузки, создайте манифест службы `internal-lb.yaml` с типом службы *LoadBalancer* и заметкой *azure-load-balancer-internal*, как показано в следующем примере.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Разверните внутреннюю подсистему балансировки нагрузки с помощью [kubectl Apply][kubectl-apply] и укажите имя манифеста YAML:

```console
kubectl apply -f internal-lb.yaml
```

Балансировщик нагрузки Azure создается в группе ресурсов узла и подключается к той же виртуальной сети, что и кластер AKS.

При просмотре сведений о службе в столбце IP-адрес внутренней подсистемы балансировки нагрузки отображается в столбце *EXTERNAL-IP*. В этом контексте *внешне* относится к внешнему интерфейсу балансировщика нагрузки, а не к получению общедоступного внешнего IP-адреса. Для превращения *\<ожидающего\>* IP-адреса в фактический внутренний IP-адрес может потребоваться пара минут, как показано в следующем примере.

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Укажите IP-адрес

Если вы хотите использовать определенный IP-адрес для внутренней подсистемы балансировки нагрузки, добавьте в ее YAML-файл манифеста свойство *loadBalancerIP*. Указанный IP-адрес должен находиться в той же подсети, что и кластер AKS, и не должен быть назначен какому-либо ресурсу.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

При развертывании и просмотре сведений о службе IP-адрес в столбце *External-IP* отражает указанный IP-адрес:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Использование частных сетей

При создании кластера AKS вы можете указать дополнительные параметры сети. Такой подход позволяет развернуть кластер в существующей виртуальной сети и подсети (подсетях). Один из вариантов — развернуть кластер AKS в частной сети, подключенной к локальной среде, и предоставить только внутренний доступ к запущенным службам. Дополнительные сведения см. в статье Настройка собственных подсетей виртуальной сети с помощью [кубенет][use-kubenet] или [Azure CNI][advanced-networking].

При развертывания внутренней подсистемы балансировки нагрузки в кластере AKS, который использует частную сеть, можно применить описанную выше процедуру без каких-либо изменений. Подсистема балансировки нагрузки создается в той же группе ресурсов, где расположен кластер AKS, но она подключается к виртуальной частной сети и подсети, как показано в следующем примере:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Возможно, субъекту-службе для кластера AKS потребуется предоставить роль *участника сети* в группе ресурсов, где развернуты ресурсы виртуальной сети Azure. Просмотрите субъект-службу с помощью команды [AZ AKS][az-aks-show](Показать `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`), например. Чтобы создать назначение ролей, используйте команду [AZ Role назначение Create][az-role-assignment-create] .

## <a name="specify-a-different-subnet"></a>Назначение другой подсети

Чтобы указать подсеть для подсистемы балансировки нагрузки, добавьте к вашей службе заметку *azure-load-balancer-internal-subnet*. Здесь нужно указать подсеть в той же виртуальной сети, где расположен кластер AKS. При развертывании адрес *EXTERNAL-IP* для подсистемы балансировки нагрузки назначается из указанной подсети.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>удаление подсистемы балансировки нагрузки

Если удалены все службы, использующие внутреннюю подсистему балансировки нагрузки, сама подсистема также удаляется.

Вы можете также напрямую удалить службу, как и любой ресурс Kubernetes, например `kubectl delete service internal-app`. При этом также будет удалена базовая подсистема балансировки нагрузки Azure.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Kubernetes Services см. в [документации по службам Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
