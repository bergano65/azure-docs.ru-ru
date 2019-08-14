---
title: Использование статического IP-адреса с подсистемой балансировки нагрузки Службы Azure Kubernetes (AKS)
description: Сведения о создании и использовании статического IP-адреса с подсистемой балансировки нагрузки Службы Azure Kubernetes (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 9e32715766734bcbb150d70aeed2dc5b06a4bcbb
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2019
ms.locfileid: "67614469"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Использование статического общедоступного IP-адреса с подсистемой балансировки нагрузки Службы Azure Kubernetes (AKS)

По умолчанию общедоступный IP-адрес, назначенный ресурсу подсистемы балансировки нагрузки, созданному кластером AKS, действителен только на протяжении существования этого ресурса. Если вы удалите службу Kubernetes, связанные с ней подсистема балансировки нагрузки и IP-адрес также будут удалены. Если вы хотите назначить определенный IP-адрес или сохранить IP-адрес для повторно развернутых служб Kubernetes, можно создать и использовать статический общедоступный IP-адрес.

В этой статье описывается создание статического общедоступного IP-адреса и его назначение службе Kubernetes.

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, ознакомьтесь с кратким руководством по AKS, [используя Azure CLI][aks-quickstart-cli] или [с помощью портал Azure][aks-quickstart-portal].

Также требуется Azure CLI версии 2.0.59 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если необходимо установить или обновить, см. раздел [install Azure CLI][install-azure-cli].

Сейчас поддерживается только *Базовый номер SKU IP*. Выполняется работа для поддержки SKU стандартного ресурса *IP* . Дополнительные сведения см. [в разделе Типы IP-адресов и методы распределения в Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Создание статического IP-адреса

Статический общедоступный IP-адрес для использования с AKS нужно создать в группе ресурсов **узла**. Если вы хотите отделить ресурсы, см. следующий раздел, чтобы [использовать статический IP-адрес за пределами группы ресурсов узла](#use-a-static-ip-address-outside-of-the-node-resource-group).

Сначала получите имя группы ресурсов узла с помощью команды [AZ AKS показывать][az-aks-show] и добавьте `--query nodeResourceGroup` параметр запроса. В следующем примере возвращается группа ресурсов узла для имени кластера AKS *myAKSCluster* в группе ресурсов *myResourceGroup*.

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Теперь создайте статический общедоступный IP-адрес с помощью команды [AZ Network public IP Create][az-network-public-ip-create] . Укажите имя группы ресурсов узла, полученное в предыдущей команде, а затем имя ресурса IP-адреса, например *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Отобразится IP-адрес, как показано в приведенном ниже примере вывода с уплотнением:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [...]
  }
}
```

Позже можно получить общедоступный IP-адрес с помощью команды [AZ Network public-IP List][az-network-public-ip-list] . Укажите имя группы ресурсов узла и созданный вами общедоступный IP-адрес, а затем запросите *ipAddress*, как показано в следующем примере:

```azurecli-interactive
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Создание службы со статическим IP-адресом

Чтобы создать службу со статическим общедоступным IP-адресом, добавьте свойство `loadBalancerIP` и значение статического общедоступного IP-адреса в манифест YAML. Создайте файл `load-balancer-service.yaml` и скопируйте в него следующий код YAML. Укажите собственный общедоступный IP-адрес, созданный на предыдущем шаге.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Выполните команду `kubectl apply` для создания службы и развертывания.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>Использование статического IP-адреса, который не входит в группу ресурсов узла

С помощью Kubernetes 1.10 (или более поздней версии) можно использовать статический IP-адрес, который создается за пределами группы ресурсов узла. Субъекту-службе, используемой кластером AKS, необходимо делегированное разрешение для другой группы ресурсов, как показано в следующем примере.

```azurecli-interactive
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Чтобы использовать IP-адрес за пределами группы ресурсов узла, добавьте заметку к определению службы. В следующем примере создается группа ресурсов с именем *myResourceGroup*. Укажите имя группы ресурсов.

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

## <a name="troubleshoot"></a>Устранение неполадок

Если статический IP-адрес, определенный в свойстве *лоадбаланцерип* манифеста службы Kubernetes, не существует или не был создан в группе ресурсов узла и не настроены дополнительные делегирования, то создание службы балансировщика нагрузки завершится ошибкой. Чтобы устранить неполадки, изучите события создания службы с помощью команды [kubectl описывают][kubectl-describe] . Введите имя службы, указанное в манифесте YAML, как показано в следующем примере:

```console
kubectl describe service azure-load-balancer
```

Отобразятся сведения о ресурсе службы Kubernetes. В разделе *Events* в конце выходных данных в следующем примере содержатся сведения о том, что *указанный пользователем IP-адрес найти не удалось*. В этих сценариях необходимо убедиться, что статический общедоступный IP-адрес создан в группе ресурсов узла и что в манифесте службы Kubernetes указан правильный IP-адрес.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Следующие шаги

Чтобы получить дополнительный контроль над сетевым трафиком к приложениям, можно вместо этого [создать контроллер][aks-ingress-basic]входящего трафика. Также можно [создать входной контроллер с статическим общедоступным IP-адресом][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
