---
title: Пример скрипта Azure CLI. Подписка на события группы ресурсов и фильтрация по ресурсу | Документация Майкрософт
description: Пример скрипта Azure CLI. Подписка на события группы ресурсов и фильтрация по ресурсу
services: event-grid
documentationcenter: na
author: tfitzmac
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: tomfitz
ms.openlocfilehash: bc058baa01021f94cf5906dd492153a8d367cc90
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51033985"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-azure-cli"></a>Подписка на события группы ресурсов и фильтрация событий для ресурса с использованием Azure CLI

С помощью этого скрипта в службе "Сетка событий" создается подписка на события, связанные с группой ресурсов. Он использует фильтр, чтобы получить события для указанного ресурса в группе ресурсов.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Для примера скрипта предварительной версии требуется расширение службы "Сетка событий". Чтобы установить его, выполните команду `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Пример скрипта — стабильная версия

[!code-azurecli[main](../../../cli_scripts/event-grid/filter-events/filter-events.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>Пример скрипта — расширение предварительной версии

[!code-azurecli[main](../../../cli_scripts/event-grid/filter-events-preview/filter-events-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать подписку на события, в скрипте используются указанные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | создание подписки в службе "Сетка событий"; |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) — версия расширения | создание подписки в службе "Сетка событий"; |

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения см. в разделе [Запрос к подпискам службы "Сетка событий Azure"](../query-event-subscriptions.md).
* Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).
