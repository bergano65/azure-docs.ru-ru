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
ms.openlocfilehash: e32b55ed655b1e47f85640eb7f494a89f3274667
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456737"
---
Мы предоставляем различные шаблоны для конкретных сценариев адаптации. Выберите наиболее подходящий вариант и обязательно измените файл параметров в соответствии со своей средой. Дополнительные сведения о том, как использовать эти файлы в развертывании, см. в статье о [подключении клиентов к системе делегированного управления ресурсами Azure](../articles/lighthouse/how-to/onboard-customer.md).

| **Шаблон** | **Описание** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Подключение подписки клиента к системе делегированного управления ресурсами Azure. Для каждой подписки необходимо выполнить отдельное развертывание. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Подключение одной или нескольких групп ресурсов клиента к системе делегированного управления ресурсами Azure. С помощью **rgDelegatedResourceManagement** можно подключить одну группу ресурсов, а с помощью **multipleRgDelegatedResourceManagement** — несколько групп ресурсов в одной подписке. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Если вы [опубликовали предложение управляемых служб в Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md), с помощью этого шаблона можно адаптировать ресурсы для клиентов, которые приняли предложение. Значения marketplace в файле параметров должны совпадать со значениями, которые использовались при публикации предложения. |

Как правило, для каждой подписки требуется отдельное развертывание. Но можно развернуть шаблоны и для нескольких подписок.

| **Шаблон** | **Описание** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Развертывание шаблонов Azure Resource Manager в нескольких подписках. |
