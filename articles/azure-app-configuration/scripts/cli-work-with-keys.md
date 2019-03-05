---
title: Пример скрипта Azure CLI. Использование пар "ключ-значение" в хранилище конфигураций приложения Azure | Документация Майкрософт
description: Сведения об использовании пар "ключ-значение" в хранилище конфигураций приложения Azure
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
ms.openlocfilehash: e69e2ca5ccd8e8edc2f55d74a0cca03eaabc9f49
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884249"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Использование пар "ключ-значение" в хранилище конфигураций приложения Azure

Этот пример скрипта создает пару "ключ-значение" в хранилище конфигураций приложения Azure, выводит список всех существующих пар "ключ-значение", обновляет значение для нового ключа и, наконец, удаляет это значение.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

Сначала необходимо установить расширение CLI для службы "Конфигурация приложений Azure", выполнив следующую команду:

        az extension add -n appconfig

## <a name="sample-script"></a>Пример скрипта

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

В этом скрипте используются приведенные ниже команды для работы с парами "ключ-значение" в хранилище конфигураций приложения. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az appconfig kv set](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-kv-set) | Создает или обновляет пары "ключ-значение". |
| [az appconfig kv list](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-kv-list) | Выводит список пар "ключ-значение" в хранилище конфигураций приложения. |
| [az appconfig kv delete](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-kv-delete) | Удаляет пару "ключ-значение". |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры скриптов Azure CLI для службы "Конфигурации приложений" см. в [документации по службе "Конфигурация приложений Azure"](../cli-samples.md).
