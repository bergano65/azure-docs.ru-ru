---
title: Используйте статический IP-адрес и dNS-метку с балансивизатором нагрузки Службы Azure Kubernetes (AKS)
description: Сведения о создании и использовании статического IP-адреса с подсистемой балансировки нагрузки Службы Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: 6c219976db21fb05ea1ad313b4effdf95906f986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047957"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Используйте статический общедоступный IP-адрес и метку DNS с балансивизатором нагрузки Службы Azure Kubernetes (AKS)

По умолчанию общедоступный IP-адрес, назначенный ресурсу подсистемы балансировки нагрузки, созданному кластером AKS, действителен только на протяжении существования этого ресурса. Если вы удалите службу Kubernetes, связанные с ней подсистема балансировки нагрузки и IP-адрес также будут удалены. Если вы хотите назначить определенный IP-адрес или сохранить IP-адрес для повторно развернутых служб Kubernetes, можно создать и использовать статический общедоступный IP-адрес.

В этой статье описывается создание статического общедоступного IP-адреса и его назначение службе Kubernetes.

## <a name="before-you-begin"></a>Перед началом

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, обратитесь к этому краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

Вам также нужна версия Azure CLI 2.0.59 или более поздняя установка и настройка. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

Эта статья охватывает использование *стандартного* SKU IP со *стандартным* балансом нагрузки SKU. Для получения дополнительной информации смотрите [типы IP-адресов и методы распределения в Azure.][ip-sku]

## <a name="create-a-static-ip-address"></a>Создание статического IP-адреса

Создание статического общедоступного IP-адреса с [помощью общедоступной команды IP-адреса сети az.][az-network-public-ip-create] Следующие создает статический IP-ресурс под названием *myAKSPublicIP* в группе ресурсов *myResourceGroup:*

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Если вы используете балансирумы нагрузки *Basic* SKU в кластере AKS, используйте *Basic* для параметра *sku* при определении общедоступного IP-адреса. Только *базовые* ИП SKU работают с балансистом нагрузки *Basic* SKU, и только *стандартные* ИП SKU работают со балансилерами нагрузки *Standard* SKU. 

Отображается IP-адрес, как показано на следующем выходе сжатого примера:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

Позже можно получить общедоступный IP-адрес с помощью команды [az network public-ip list][az-network-public-ip-list]. Укажите имя группы ресурсов узла и созданный вами общедоступный IP-адрес, а затем запросите *ipAddress*, как показано в следующем примере:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Создание службы со статическим IP-адресом

Перед созданием службы убедитесь, что основной службы, используемый кластером AKS, делегировал разрешения другой группе ресурсов. Пример:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Кроме того, для получения разрешений можно использовать систему, назначенную управляемой личностью, а не для основного обслуживания. Для получения дополнительной информации [см.](use-managed-identity.md)

Чтобы создать службу *LoadBalancer* со статическим `loadBalancerIP` общедоступным IP-адресом, добавьте свойство и значение статического общедоступного IP-адреса в манифест YAML. Создайте файл `load-balancer-service.yaml` и скопируйте в него следующий код YAML. Укажите собственный общедоступный IP-адрес, созданный на предыдущем шаге. Следующий пример также устанавливает аннотацию к группе ресурсов под названием *myResourceGroup*. Укажите свое собственное название группы ресурсов.

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

Выполните команду `kubectl apply` для создания службы и развертывания.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>Применить метку DNS к службе

Если служба использует динамический или статический общедоступный IP-адрес, вы можете использовать аннотацию `service.beta.kubernetes.io/azure-dns-label-name` службы для установки общедоступной метки DNS. При этом публикуется полностью квалифицированное доменное имя для службы с помощью общедоступных DNS-серверов Azure и домена верхнего уровня. Значение аннотации должно быть уникальным в расположении Azure, поэтому рекомендуется использовать достаточно квалифицированную метку.   

Затем Azure автоматически приговыт подсеть `<location>.cloudapp.azure.com` по умолчанию, например (где место является выбранным вами регионом), чтобы создать полностью квалифицированное имя DNS. Пример:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> Чтобы опубликовать сервис на своем домене, [см.][azure-dns-zone] [external-dns][external-dns]

## <a name="troubleshoot"></a>Устранение неполадок

Если статического IP-адреса, определенного в свойстве *нагрузкиBalancerIP* манифеста службы Kubernetes, не существует или не создан в группе ресурсов узлов и не настроены дополнительные делегации, создание службы балансировора нагрузки завершается неудачей. Для устранения неполадок просмотрите события создания службы, выполнив команду [kubectl describe][kubectl-describe]. Введите имя службы, указанное в манифесте YAML, как показано в следующем примере:

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

## <a name="next-steps"></a>Дальнейшие действия

Для дополнительного управления входящим трафиком приложений можно [создать контроллер входящего трафика][aks-ingress-basic]. Вы также можете [создать контроллер входящего трафика со статическим общедоступным IP-адресом][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

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
