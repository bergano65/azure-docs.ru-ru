---
title: Подписка на события учетной записи хранения больших двоичных объектов в Azure PowerShell
description: В этой статье приведен пример скрипта Azure PowerShell, в котором показано, как подписаться на события Сетки событий для учетной записи хранилища BLOB-объектов.
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: 6e1d7fa349134400c26e03815983067d921f388c
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171233"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>Создание подписки на события, связанные с учетной записью хранения больших двоичных объектов, с помощью PowerShell

С помощью этого скрипта в службе "Сетка событий" создается подписка на события, связанные с учетной записью хранения больших двоичных объектов.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Пример скрипта — стабильная версия

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.ps1 "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать подписку на события, в скрипте используются указанные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | создание подписки в службе "Сетка событий"; |

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения об управляемых приложениях Azure см. в [этой статье](../overview.md).
* Дополнительные сведения см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
