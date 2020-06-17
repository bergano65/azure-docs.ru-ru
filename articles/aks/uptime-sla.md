---
title: Служба Azure Kubernetes (AKS) с Соглашением об уровне обслуживания с гарантией времени доступности
description: Сведения о варианте Соглашения об уровне обслуживания с гарантией времени доступности для сервера API Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: references_regions
ms.openlocfilehash: 2df0ad675f03b25363ab0f5b13dceb762a657ed7
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84299559"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Соглашение об уровне обслуживания с гарантией времени доступности для Службы Azure Kubernetes (AKS)

Соглашение об уровне обслуживания с гарантией времени доступности — это возможность за дополнительную плату получить более высокий уровень обслуживания кластера. Соглашение об уровне обслуживания с гарантией времени доступности гарантирует 99,95% доступности для конечной точки сервера Kubernetes API любому кластеру, который использует [Зоны доступности][availability-zones], или 99,9% доступности для кластеров, которые не используют Зоны доступности. AKS использует реплики главного узла в доменах обновления и сбоя, чтобы обеспечить соблюдение Соглашения об уровне обслуживания.

Такое соглашение предназначено для клиентов, которым оно требуется для соблюдения нормативных требований или охвата его условиями конечных пользователей. Оно также может быть полезным для клиентов с критически важными рабочими нагрузками. Вариант Соглашения об уровне обслуживания с гарантией времени доступности совместно с Зонами доступности обеспечивает более высокий уровень доступности для сервера API Kubernetes.  

Клиенты по-прежнему смогут создавать неограниченное число бесплатных кластеров с целевым уровнем обслуживания 99,5% и по своему усмотрению выбирать Цель уровня обслуживания или Соглашение об уровне обслуживания с гарантией времени доступности.

> [!Important]
> Для кластеров с блокировкой исходящего трафика необходимо [открыть соответствующие порты](limit-egress-traffic.md).

## <a name="sla-terms-and-conditions"></a>Условия Соглашения об уровне обслуживания

Соглашение об уровне обслуживания с гарантией времени доступности является платной возможностью и включается отдельно для каждого кластера. Цены на Соглашение об уровне обслуживания с гарантией времени доступности зависят от количества отдельных кластеров, но не от их размеров. Дополнительные сведения см. на странице [Цены на службу Azure Kubernetes (AKS)](https://azure.microsoft.com/pricing/details/kubernetes-service/).

## <a name="before-you-begin"></a>Перед началом

* Azure CLI 2.7.0 или более поздней версии

## <a name="creating-a-cluster-with-uptime-sla"></a>Создание кластера, защищенного Соглашением об уровне обслуживания с гарантией времени доступности

Чтобы создать новый кластер, защищенный Соглашением об уровне обслуживания с гарантией времени доступности, используйте Azure CLI.

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
Используйте команду [az aks create][az-aks-create], чтобы создать кластер AKS. В следующем примере создается кластер *myAKSCluster* с одним узлом. Также с помощью параметра *--enable-addons monitoring* можно включить Azure Monitor для контейнеров.  Эта операция занимает несколько минут.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
Через несколько минут выполнение команды завершается и отображаются сведения о кластере в формате JSON. Следующий фрагмент кода JSON определяет для кластера платный уровень ценовой категории и применение Соглашения об уровне обслуживания с гарантией времени доступности.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="limitations"></a>Ограничения

* В настоящее время нельзя применить Соглашения об уровне обслуживания с гарантией времени доступности к существующему кластеру.
* Сейчас нельзя отменить Соглашение об уровне обслуживания с гарантией времени доступности для кластера AKS, если эта возможность была активирована при его создании.  
* Пользовательские кластеры в настоящее время не поддерживаются.

## <a name="next-steps"></a>Дальнейшие действия

Примените [Зоны доступности][availability-zones], чтобы повысить уровень доступности для рабочих нагрузок кластера AKS.
Настройте для кластера [ограничение исходящего трафика](limit-egress-traffic.md).

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
