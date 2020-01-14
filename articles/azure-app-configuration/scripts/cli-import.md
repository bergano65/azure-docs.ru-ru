---
title: Пример скрипта Azure CLI. Импорт в хранилище службы "Конфигурация приложений"
titleSuffix: Azure App Configuration
description: Информация и примеры скриптов для импорта данных в хранилище конфигураций приложения Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 28c04859f23b3a560166aa62ac903e1204fc5d97
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75413292"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Импорт в хранилище конфигураций приложения Azure

Этот пример скрипта импортирует параметры пары "ключ — значение" в хранилище службы "Конфигурация приложений Azure".

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки Azure локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Чтобы выполнить установку или обновление Azure CLI, ознакомьтесь с [этой статьей](/cli/azure/install-azure-cli).

Сначала необходимо установить расширение CLI для службы "Конфигурация приложений Azure", выполнив следующую команду:

        az extension add -n appconfig

## <a name="sample-script"></a>Пример скрипта

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

В этом скрипте используются следующие команды для импорта в хранилище службы "Конфигурация приложений". Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az appconfig kv import](/cli/azure/ext/appconfig/appconfig/kv#ext-appconfig-az-appconfig-kv-import) | Импортирует данные в ресурс хранилища службы "Конфигурация приложений". |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры скриптов Azure CLI для службы "Конфигурация приложений" см. в [этой статье](../cli-samples.md).
