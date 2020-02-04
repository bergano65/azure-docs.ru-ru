---
title: Пример скрипта Azure CLI. Создание пользовательского раздела | Документация Майкрософт
description: В этой статье приведен пример скрипта Azure CLI, в котором показано, как создать пользовательский раздел в службе "Сетка событий Azure".
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2c6e027d234104d5e45c632dd73e79d8f069deb5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720849"
---
# <a name="create-event-grid-custom-topic-with-azure-cli"></a>Создание пользовательского раздела службы "Сетка событий" с помощью Azure CLI

С помощью этого скрипта создается пользовательский раздел службы "Сетка событий".

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/event-grid/create-custom-topic/create-custom-topic.sh "Create custom topic")]

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать пользовательский раздел, в скрипте используются указанные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az eventgrid topic create](https://docs.microsoft.com/cli/azure/eventgrid/topic#az-eventgrid-topic-create) | Создание пользовательского раздела службы "Сетка событий". |


## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения см. в разделе [Запрос к подпискам службы "Сетка событий Azure"](../query-event-subscriptions.md).
* Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).
