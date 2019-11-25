---
title: Service tiers and SKUs
description: Learn about the features and limits in the Basic, Standard, and Premium service tiers (SKUs) of Azure Container Registry.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456270"
---
# <a name="azure-container-registry-skus"></a>Номера SKU реестра контейнеров Azure

Реестр контейнеров Azure (ACR) доступен на нескольких уровнях служб, известных как номера SKU. Эти номера SKU предоставляют прогнозируемые цены и несколько вариантов, относящихся к согласованию с емкостью и шаблонами потребления частного реестра Docker в Azure.

| SKU | Описание |
| --- | ----------- |
| **базовая;** | Недорогой начальный уровень для разработчиков, изучающих реестр контейнеров Azure. Basic registries have the same programmatic capabilities as Standard and Premium (such as Azure Active Directory [authentication integration](container-registry-authentication.md#individual-login-with-azure-ad), [image deletion][container-registry-delete], and [webhooks][container-registry-webhook]). Тем не менее поставляемые хранилище и пропускная способность образов подходят для сценариев с меньшим коэффициентом использования. |
| **Стандартный** | Реестры уровня "Стандартный" предоставляют те же возможности, что и реестры уровня "Базовый", но больший размер хранилища и более высокую пропускную способность образов. Реестры уровня "Стандартный" подходят для большинства рабочих сценариев. |
| **Премиальный** | В реестрах уровня "Премиум" обеспечивается наивысший объем хранилища и количество параллельных операций, что делает возможными сценарии с большим объемом данных. In addition to higher image throughput, Premium adds features such as [geo-replication][container-registry-geo-replication] for managing a single registry across multiple regions, [content trust](container-registry-content-trust.md) for image tag signing, [firewalls and virtual networks (preview)](container-registry-vnet.md) to restrict access to the registry. |

The Basic, Standard, and Premium SKUs all provide the same programmatic capabilities. They also all benefit from [image storage][container-registry-storage] managed entirely by Azure. Выбрав номер SKU более высокого уровня, можно получить больший уровень производительности и масштабируемости. При наличии нескольких уровней служб можно начать работу с "Базового" уровня служб, а затем перейти к уровню "Стандартный" и "Премиум" по мере увеличения потребления реестра.

## <a name="sku-features-and-limits"></a>SKU features and limits

В следующей таблице приведены сведения о возможностях и ограничениях уровней служб "Базовый", "Стандартный" и "Премиум".

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Изменение номеров SKU

Номер SKU реестра можно изменить с помощью Azure CLI или на портале Azure. You can move freely between SKUs as long as the SKU you're switching to has the required maximum storage capacity. 

### <a name="azure-cli"></a>Azure CLI

To move between SKUs in the Azure CLI, use the [az acr update][az-acr-update] command. Например, чтобы перейти на уровень обслуживания "Премиум":

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>портала Azure

В колонке **Обзор** реестра контейнеров на портале Azure выберите **Обновить**, а затем выберите новый **номер SKU** из раскрывающегося списка номеров SKU.

![Обновление номера SKU реестра контейнеров на портале Azure][update-registry-sku]

## <a name="pricing"></a>Стоимость

Сведения о ценах на каждый номер SKU реестра контейнеров Azure см. на странице [цен на реестр контейнеров][container-registry-pricing].

Дополнительные сведения о стоимости передачи данных см. на [странице цен на пропускную способность](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Дальнейшие действия

**Схема развития реестра контейнеров Azure**

См. [схему развития ACR][acr-roadmap] в GitHub, чтобы найти сведения о новых возможностях в службе.

**UserVoice реестра контейнеров Azure**

Отправляйте свои предложения и голосуйте за предложения функций на [форуме UserVoice для ACR][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
