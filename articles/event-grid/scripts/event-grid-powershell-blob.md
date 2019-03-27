---
title: Пример скрипта Azure PowerShell. Подписка на учетную запись хранения больших двоичных объектов | Документация Майкрософт
description: Пример скрипта Azure PowerShell. Подписка на события учетной записи хранения больших двоичных объектов
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
ms.openlocfilehash: d236bcbf71915576ee31587e9abfff92c99250c1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58177523"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>Создание подписки на события, связанные с учетной записью хранения больших двоичных объектов, с помощью PowerShell

С помощью этого скрипта в службе "Сетка событий" создается подписка на события, связанные с учетной записью хранения больших двоичных объектов.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Пример скрипта — стабильная версия

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.ps1 "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать подписку на события, в скрипте используются указанные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | создание подписки в службе "Сетка событий"; |

## <a name="next-steps"></a>Дополнительная информация

* Общие сведения об управляемых приложениях Azure см. в [этой статье](../overview.md).
* Дополнительные сведения см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
