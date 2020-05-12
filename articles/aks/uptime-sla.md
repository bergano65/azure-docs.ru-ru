---
title: Высокая доступность службы Azure Kubernetes Service (AKS) с соглашением об уровне обслуживания с временем доступности
description: Узнайте о необязательном предложении соглашения об уровне обслуживания с высоким уровнем доступности для сервера API Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 1c340f85a107cac437e1241025d8c9bc6991b965
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125729"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Соглашение об уровне обслуживания для времени доступности службы Azure Kubernetes (AKS)

Соглашение об уровне обслуживания для времени работы — это необязательная функция, позволяющая обеспечить финансово высокий уровень соглашения об уровне обслуживания для кластера. Соглашение об уровне обслуживания гарантирует 99,95% доступности конечной точки сервера Kubernetes API для кластеров, использующих [зону доступности][availability-zones] и 99,9% доступности для кластеров, которые не используют зоны доступности. AKS использует реплики главного узла в доменах обновления и сбоя, чтобы обеспечить соблюдение требований соглашения об уровне обслуживания.

Клиенты, которым требуется соглашение об уровне обслуживания для соответствия требованиям, или расширение соглашения об уровне обслуживания для их клиентов, должны включить эту функцию. Клиенты с критически важными рабочими нагрузками, которым требуется более высокий уровень доступности с возможностью соглашения об уровне обслуживания, могут использовать эту функцию. Включите функцию с Зоны доступности, чтобы получить более высокую доступность сервера API Kubernetes.  

Клиенты могут создавать неограниченные свободные кластеры с целью уровня обслуживания (цели) 99,5%.

> [!Important]
> Для кластеров с блокировкой исходящих данных см. раздел [ограничение исходящего трафика](limit-egress-traffic.md) , чтобы открыть соответствующие порты для соглашения об уровне обслуживания времени работы.

## <a name="sla-terms-and-conditions"></a>Условия соглашения об уровне обслуживания

Соглашение об уровне обслуживания "время работы" является платным компонентом и включается для каждого кластера. Цены на время использования соглашения об уровне обслуживания определяются количеством кластеров, а не размером кластеров. Для получения дополнительных сведений можно просмотреть [сведения о ценах соглашения об уровне обслуживания](https://azure.microsoft.com/pricing/details/kubernetes-service/) .

## <a name="region-availability"></a>Доступность по регионам

Соглашение об уровне обслуживания для времени работы доступно в следующих регионах:

* Восточная Австралия
* Центральная Канада
* Восточная часть США
* восточная часть США 2
* Центрально-южная часть США
* Юго-Восточная Азия
* западная часть США 2

## <a name="before-you-begin"></a>Перед началом

* Azure CLI версии 2.7.0 или более поздней.

## <a name="creating-a-cluster-with-uptime-sla"></a>Создание кластера с соглашением об уровне обслуживания для времени работы

Чтобы создать новый кластер с соглашением об уровне обслуживания для времени работы, используйте Azure CLI.

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
Используйте команду [az aks create][az-aks-create], чтобы создать кластер AKS. В следующем примере создается кластер *myAKSCluster* с одним узлом. Также с помощью параметра *--enable-addons monitoring* можно включить Azure Monitor для контейнеров.  Эта операция занимает несколько минут.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
Через несколько минут выполнение команды завершается и отображаются сведения о кластере в формате JSON. В следующем фрагменте кода JSON показан платный уровень номера SKU, указывающий, что кластер включен с соглашением об уровне обслуживания времени работы.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters",
  "windowsProfile": null
```

## <a name="limitations"></a>Ограничения

* Сейчас невозможно добавить соглашение об уровне обслуживания "время работы" в существующие кластеры.
* В настоящее время невозможно удалить соглашение об уровне обслуживания для времени работы из кластера AKS.  

## <a name="next-steps"></a>Дальнейшие действия

Используйте [зоны доступности][availability-zones] , чтобы повысить уровень доступности для рабочих нагрузок кластера AKS.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
