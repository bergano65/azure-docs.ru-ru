---
title: Пример скрипта Azure CLI. Использование пар "ключ — значение" в хранилище конфигураций приложения Azure
titleSuffix: Azure App Configuration
description: Сведения об использовании пар "ключ-значение" в хранилище конфигураций приложения Azure
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 11/08/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 931c2f738556404e2d58381f7152acb0613a057b
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77468190"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Использование пар "ключ-значение" в хранилище конфигураций приложения Azure

Этот пример скрипта создает пару "ключ-значение" в хранилище конфигураций приложения Azure, выводит список всех существующих пар "ключ-значение", обновляет значение для нового ключа и, наконец, удаляет это значение.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Пример скрипта

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

В этом скрипте используются приведенные ниже команды для работы с парами "ключ —значение" в хранилище Конфигурации приложений. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
|---|---|
| [az appconfig kv set](/cli/azure/appconfig/kv#az-appconfig-kv-set) | Создает или обновляет пары "ключ-значение". |
| [az appconfig kv list](/cli/azure/appconfig/kv#az-appconfig-kv-list) | Выводит список пар "ключ —значение" в хранилище Конфигурации приложений. |
| [az appconfig kv delete](/cli/azure/appconfig/kv#az-appconfig-kv-delete) | Удаляет пару "ключ-значение". |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры скриптов Azure CLI для службы "Конфигурация приложений" см. в [этой статье](../cli-samples.md).
