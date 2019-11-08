---
title: Пример скрипта Azure CLI. Подписка на события подписки Azure | Документация Майкрософт
description: Пример скрипта Azure CLI. Подписка на события подписки Azure
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
ms.openlocfilehash: 9d1aa53ede323c2bb536c74eeaaba9fd28b01712
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580390"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-azure-cli"></a>Создание подписки на события, связанные с подпиской Azure, с использованием Azure CLI

С помощью этого скрипта в службе "Сетка событий" создается подписка на события, связанные с подпиской Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Для примера скрипта предварительной версии требуется расширение службы "Сетка событий". Чтобы установить его, выполните команду `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Пример скрипта — стабильная версия

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>Пример скрипта — расширение предварительной версии

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать подписку на события, в скрипте используются указанные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | создание подписки в службе "Сетка событий"; |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) — версия расширения | создание подписки в службе "Сетка событий"; |

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения см. в разделе [Запрос к подпискам службы "Сетка событий Azure"](../query-event-subscriptions.md).
* Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).
