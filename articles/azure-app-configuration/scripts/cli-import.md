---
title: Пример скрипта Azure CLI. Импорт в хранилище конфигураций для приложения Azure | Документация Майкрософт
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
ms.openlocfilehash: 65fa09b779f656654411a1d0c3602598655f50c9
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884366"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Импорт в хранилище конфигураций приложения Azure

Этот пример скрипта импортирует пары "ключ-значение" в хранилище конфигураций приложения Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

Сначала необходимо установить расширение CLI для службы "Конфигурация приложений Azure", выполнив следующую команду:

        az extension add -n appconfig

## <a name="sample-script"></a>Пример скрипта

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --file ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

В этом скрипте используются следующие команды для импорта в хранилище конфигураций приложения. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az appconfig import](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-import) | Импортирует данные в ресурс хранилища конфигураций приложения. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры скриптов Azure CLI для службы "Конфигурации приложений" см. в [документации по службе "Конфигурация приложений Azure"](../cli-samples.md).
