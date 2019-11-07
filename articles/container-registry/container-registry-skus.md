---
title: Номера SKU реестра контейнеров Azure
description: Сведения о функциях и ограничениях для уровней обслуживания "базовый", "Стандартный" и "Премиум" для реестра контейнеров Azure.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 11/05/2019
ms.author: danlep
ms.openlocfilehash: 19b1fb78413f82d422779b12227b4a5e2361d813
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681808"
---
# <a name="azure-container-registry-skus"></a>Номера SKU реестра контейнеров Azure

Реестр контейнеров Azure (ACR) доступен на нескольких уровнях служб, известных как номера SKU. Эти номера SKU предоставляют прогнозируемые цены и несколько вариантов, относящихся к согласованию с емкостью и шаблонами потребления частного реестра Docker в Azure.

| SKU | Description (Описание) |
| --- | ----------- |
| **базовая;** | Недорогой начальный уровень для разработчиков, изучающих реестр контейнеров Azure. Основные реестры имеют те же программные возможности, что и стандартные и Premium (например, [интеграция Azure Active Directory проверки подлинности](container-registry-authentication.md#individual-login-with-azure-ad), [Удаление образа][container-registry-delete]и [веб-перехватчики][container-registry-webhook]). Тем не менее поставляемые хранилище и пропускная способность образов подходят для сценариев с меньшим коэффициентом использования. |
| **Стандартный** | Реестры уровня "Стандартный" предоставляют те же возможности, что и реестры уровня "Базовый", но больший размер хранилища и более высокую пропускную способность образов. Реестры уровня "Стандартный" подходят для большинства рабочих сценариев. |
| **Премиальный** | В реестрах уровня "Премиум" обеспечивается наивысший объем хранилища и количество параллельных операций, что делает возможными сценарии с большим объемом данных. В дополнение к более высокой пропускной способности образа Premium добавляет такие функции, как [Георепликация][container-registry-geo-replication] для управления одним реестром в нескольких регионах, [отношение доверия](container-registry-content-trust.md) с тегом образа для подписывания тегов изображений, [брандмауэры и виртуальные сети (Предварительная версия)](container-registry-vnet.md) . Ограничьте доступ к реестру. |

Номера SKU "базовый", "Стандартный" и "Премиум" предоставляют одни и те же программные возможности. Кроме того, все преимущества [хранилища образов][container-registry-storage] , которыми управляет Azure, полностью. Выбрав номер SKU более высокого уровня, можно получить больший уровень производительности и масштабируемости. При наличии нескольких уровней служб можно начать работу с "Базового" уровня служб, а затем перейти к уровню "Стандартный" и "Премиум" по мере увеличения потребления реестра.

## <a name="sku-features-and-limits"></a>Функции и ограничения SKU

В следующей таблице приведены сведения о возможностях и ограничениях уровней служб "Базовый", "Стандартный" и "Премиум".

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Изменение номеров SKU

Номер SKU реестра можно изменить с помощью Azure CLI или на портале Azure. Можно свободно перемещаться между номерами SKU при условии, что номер SKU, к которому осуществляется переключение, требует максимального объема хранилища. 

### <a name="azure-cli"></a>Azure CLI

Для перехода между номерами SKU в Azure CLI используйте команду [AZ контроля обновления][az-acr-update] . Например, чтобы перейти на уровень обслуживания "Премиум":

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
