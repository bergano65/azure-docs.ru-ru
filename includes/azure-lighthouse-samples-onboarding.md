---
title: включить файл
description: включить файл
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 6b873508fe29ed0816a8b64b26cc5522ed23a8d6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986697"
---
Мы предоставляем различные шаблоны для конкретных сценариев адаптации. Выберите наиболее подходящий вариант и обязательно измените файл параметров в соответствии со своей средой. Дополнительные сведения о том, как использовать эти файлы в развертывании, см. в статье о [подключении клиентов к системе делегированного управления ресурсами Azure](../articles/lighthouse/how-to/onboard-customer.md).

| **Шаблон** | **Описание** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Подключение подписки клиента к системе делегированного управления ресурсами Azure. Для каждой подписки необходимо выполнить отдельное развертывание. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Подключение одной или нескольких групп ресурсов клиента к системе делегированного управления ресурсами Azure. С помощью **rgDelegatedResourceManagement** можно подключить одну группу ресурсов, а с помощью **multipleRgDelegatedResourceManagement** — несколько групп ресурсов в одной подписке. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Если вы [опубликовали предложение управляемых служб в Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md), с помощью этого шаблона можно адаптировать ресурсы для клиентов, которые приняли предложение. Значения marketplace в файле параметров должны совпадать со значениями, которые использовались при публикации предложения. |

Как правило, для каждой подписки требуется отдельное развертывание. Но можно развернуть шаблоны и для нескольких подписок.

| **Шаблон** | **Описание** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Развертывание шаблонов Azure Resource Manager в нескольких подписках. |
