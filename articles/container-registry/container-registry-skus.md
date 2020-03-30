---
title: Уровни обслуживания и SkUS
description: Узнайте об особенностях и ограничениях в базовых, стандартных и премиальных уровнях обслуживания (SСБУ) реестра контейнеров Azure.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456270"
---
# <a name="azure-container-registry-skus"></a>Номера SKU реестра контейнеров Azure

Реестр контейнеров Azure (ACR) доступен на нескольких уровнях служб, известных как номера SKU. Эти номера SKU предоставляют прогнозируемые цены и несколько вариантов, относящихся к согласованию с емкостью и шаблонами потребления частного реестра Docker в Azure.

| номер SKU | Описание |
| --- | ----------- |
| **Basic** | Недорогой начальный уровень для разработчиков, изучающих реестр контейнеров Azure. Основные реестры имеют те же программные возможности, что и Standard и Premium (например, [интеграция аутентификации](container-registry-authentication.md#individual-login-with-azure-ad)Azure Active Directory, [удаление изображений][container-registry-delete]и [веб-крючки).][container-registry-webhook] Тем не менее поставляемые хранилище и пропускная способность образов подходят для сценариев с меньшим коэффициентом использования. |
| **Стандартный** | Реестры уровня "Стандартный" предоставляют те же возможности, что и реестры уровня "Базовый", но больший размер хранилища и более высокую пропускную способность образов. Реестры уровня "Стандартный" подходят для большинства рабочих сценариев. |
| **Премиум** | В реестрах уровня "Премиум" обеспечивается наивысший объем хранилища и количество параллельных операций, что делает возможными сценарии с большим объемом данных. В дополнение к более высокой пропускной связи, Premium добавляет такие функции, как [георепликация][container-registry-geo-replication] для управления единым реестром в нескольких регионах, [доверие к содержимому](container-registry-content-trust.md) для подписания меток изображения, [брандмауэры и виртуальные сети (предварительный просмотр)](container-registry-vnet.md) для ограничения доступа к реестру. |

Основные, стандартные и премиальные СКУ обеспечивают одинаковые программные возможности. Все они также извлекают выгоду из [хранения изображений,][container-registry-storage] полностью управляемого Azure. Выбрав номер SKU более высокого уровня, можно получить больший уровень производительности и масштабируемости. При наличии нескольких уровней служб можно начать работу с "Базового" уровня служб, а затем перейти к уровню "Стандартный" и "Премиум" по мере увеличения потребления реестра.

## <a name="sku-features-and-limits"></a>Особенности и ограничения SKU

В следующей таблице приведены сведения о возможностях и ограничениях уровней служб "Базовый", "Стандартный" и "Премиум".

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Изменение номеров SKU

Номер SKU реестра можно изменить с помощью Azure CLI или на портале Azure. Вы можете свободно перемещаться между СКУ до тех пор, пока SKU, на который вы переключаетесь, имеет необходимую максимальную емкость. 

### <a name="azure-cli"></a>Azure CLI

Для перемещения между номерами SKU в Azure CLI используйте команду [az acr update][az-acr-update]. Например, чтобы перейти на уровень обслуживания "Премиум":

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Портал Azure

В колонке **Обзор** реестра контейнеров на портале Azure выберите **Обновить**, а затем выберите новый **номер SKU** из раскрывающегося списка номеров SKU.

![Обновление номера SKU реестра контейнеров на портале Azure][update-registry-sku]

## <a name="pricing"></a>Цены

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
