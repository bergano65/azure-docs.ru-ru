---
title: Создание внутреннего балансировщика нагрузки
titleSuffix: Azure Kubernetes Service
description: Узнайте, как создать и использовать внутреннюю подсистему балансировки нагрузки для предоставления доступа к службам через AKS (Службу Azure Kubernetes).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 0789a866ebda270f3e5e8b150e072c7aedea7f04
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82790615"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Использование внутренней подсистемы балансировки нагрузки со Службой Azure Kubernetes (AKS)

Чтобы ограничить доступ к приложениям в Службе Azure Kubernetes (AKS), можно создать и использовать внутреннюю подсистему балансировки нагрузки. Внутренняя подсистема балансировки нагрузки позволяет сделать службу Kubernetes доступной только для приложений, работающих в той же виртуальной сети, что и кластер Kubernetes. В этой статье описаны процессы создания и использования внутренней подсистемы балансировки нагрузки со Службой Azure Kubernetes (AKS).

> [!NOTE]
> Доступны два номера SKU Azure Load Balancer: ценовых категорий *Базовый* и *Стандартный*. По умолчанию при создании кластера AKS используется номер SKU ценовой категории "Стандартный" .  При создании службы с типом "подсистема балансировки нагрузки" вы получаете тот же тип балансировки нагрузки, что и при подготовке кластера. Дополнительные сведения см. в разделе [Сравнение номеров SKU для Azure Load Balancer][azure-lb-comparison].

## <a name="before-you-begin"></a>Перед началом

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, обратитесь к краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

Кроме того, нужно установить и настроить Azure CLI версии 2.0.59 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

Субъекту-службе кластера AKS требуется разрешение для управления сетевыми ресурсами, если используется существующая подсеть или группа ресурсов. В общем случае назначьте субъекту-службе роль *Участник сети* для делегированных ресурсов. Вместо субъекта-службы можно использовать управляемое удостоверение, назначенное системой, для разрешений. Дополнительные сведения см. в статье о том, [как использовать управляемые удостоверения](use-managed-identity.md). Дополнительные сведения о разрешениях см. в разделе [Делегирование прав доступа другим ресурсам Azure][aks-sp].

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

При просмотре сведений о службе в столбце IP-адрес внутренней подсистемы балансировки нагрузки отображается в столбце *EXTERNAL-IP*. В этом контексте *внешне* относится к внешнему интерфейсу балансировщика нагрузки, а не к получению общедоступного внешнего IP-адреса. Изменение IP-адреса с фактического на внутренний IP-адрес может занять одну или две минуты *\<pending\>* , как показано в следующем примере:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Указание IP-адреса

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
> Возможно, субъекту-службе для кластера AKS потребуется предоставить роль *участника сети* в группе ресурсов, где развернуты ресурсы виртуальной сети Azure. Чтобы просмотреть параметры субъекта-службы, используйте команду [az aks show][az-aks-show], например так: `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Чтобы создать назначение роли, используйте команду [az role assignment create][az-role-assignment-create].

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

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о службах Kubernetes в [документации по службам Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/skus.md
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
