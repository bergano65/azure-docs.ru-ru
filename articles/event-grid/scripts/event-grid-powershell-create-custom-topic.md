---
title: Пример скрипта Azure PowerShell. Создание пользовательского раздела | Документация Майкрософт
description: В этой статье приведен пример скрипта Azure PowerShell, в котором показано, как создать пользовательский раздел в службе "Сетка событий".
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: tomfitz
ms.openlocfilehash: 900f31ff87e6a588fc1d1e3efd854437d2c8c1b9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719948"
---
# <a name="create-event-grid-custom-topic-with-powershell"></a>Создание пользовательского раздела службы "Сетка событий" с помощью PowerShell

С помощью этого скрипта создается пользовательский раздел службы "Сетка событий".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/event-grid/create-custom-topic/create-custom-topic.ps1 "Create custom topic")]

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать пользовательский раздел, в скрипте используются указанные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [New-AzEventGridTopic](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridtopic) | Создание пользовательского раздела службы "Сетка событий". |

## <a name="next-steps"></a>Дальнейшие действия

* Общие сведения об управляемых приложениях Azure см. в [этой статье](../overview.md).
* Дополнительные сведения см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
