---
title: Пример скрипта Azure PowerShell. Подписка на события группы ресурсов | Документация Майкрософт
description: Пример скрипта Azure PowerShell. Подписка на события группы ресурсов
services: event-grid
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: tomfitz
ms.openlocfilehash: fe36336bb1bcc3b0d1cc718724ca05f6d23110c7
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270541"
---
# <a name="subscribe-to-events-for-a-resource-group-with-powershell"></a>Создание подписки на события, связанные с группой ресурсов, с использованием PowerShell

С помощью этого скрипта в службе "Сетка событий" создается подписка на события, связанные с группой ресурсов.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Для примера скрипта предварительной версии требуется модуль службы "Сетка событий". Чтобы установить его, выполните такую команду. `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>Пример скрипта — стабильная версия

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-resource-group/subscribe-to-resource-group.ps1 "Subscribe to resource group")]

## <a name="sample-script---preview-module"></a>Пример скрипта — модуль предварительной версии

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-resource-group-preview/subscribe-to-resource-group-preview.ps1 "Subscribe to resource group")]

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать подписку на события, в скрипте используются указанные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [New-AzureRmEventGridSubscription](https://docs.microsoft.com/powershell/module/azurerm.eventgrid/new-azurermeventgridsubscription) | создание подписки в службе "Сетка событий"; |

## <a name="next-steps"></a>Дополнительная информация

* Общие сведения об управляемых приложениях Azure см. в [этой статье](../overview.md).
* Дополнительные сведения см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
