---
title: Пример скрипта Azure CLI. Создание пользовательского раздела | Документация Майкрософт
description: Пример скрипта Azure CLI. Создание пользовательского раздела
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
ms.openlocfilehash: 16c6586939a6dad248cef3abdabd78faf04a2381
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580378"
---
# <a name="create-event-grid-custom-topic-with-azure-cli"></a>Создание пользовательского раздела службы "Сетка событий" с помощью Azure CLI

С помощью этого скрипта создается пользовательский раздел службы "Сетка событий".

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/event-grid/create-custom-topic/create-custom-topic.sh "Create custom topic")]

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать пользовательский раздел, в скрипте используются указанные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az eventgrid topic create](https://docs.microsoft.com/cli/azure/eventgrid/topic#az-eventgrid-topic-create) | Создание пользовательского раздела службы "Сетка событий". |


## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения см. в разделе [Запрос к подпискам службы "Сетка событий Azure"](../query-event-subscriptions.md).
* Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).
