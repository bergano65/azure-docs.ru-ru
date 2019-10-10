---
title: Пример скрипта Azure CLI. Создание хранилища конфигураций для приложения Azure | Документация Майкрософт
description: Пример скрипта Azure CLI. Создание хранилища конфигураций для приложения Azure
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
ms.openlocfilehash: 8a58b73f3f3acb8d7c6ced9e0086687b54fff734
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029796"
---
# <a name="create-an-azure-app-configuration-store"></a>Создание хранилища конфигураций для приложения Azure

В этом примере скрипта создается экземпляр конфигурации приложения Azure в новой группе ресурсов со случайным именем.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

Сначала необходимо установить расширение CLI для службы "Конфигурация приложений Azure", выполнив следующую команду:

        az extension add -n appconfig

## <a name="sample-script"></a>Пример скрипта

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
#resource name must be lowercase
myAppConfigStoreName=${appConfigName,,}
myResourceGroupName=$appConfigName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure AppConfig Service resource and query the hostName
appConfigHostname=$(az appconfig create \
  --name $myAppConfigStoreName \
  --location eastus \
  --resource-group $myResourceGroupName \
  --query hostName \
  -o tsv)

# Get the AppConfig primary key 
appConfigPrimaryKey=$(az appconfig key list --name $myAppConfigStoreName \
  --resource-group $myResourceGroupName --query primaryKey -o tsv)

# Form the connection string for use in your application
connstring="Endpoint=https://$appConfigHostname;AccessKey=$appConfigPrimaryKey;"
echo "$connstring"
```

Запишите фактическое имя, созданное для новой группы ресурсов. Это имя группы ресурсов вам понадобится для удаления групп ресурсов.

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов и хранилища конфигураций приложения этот сценарий использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az appconfig create](/cli/azure/ext/appconfig/appconfig) | Создает ресурс хранилища конфигураций приложения. |
| [az appconfig key list](/cli/azure/ext/appconfig/appconfig/kv) | Выводит список ключей, которые содержатся в хранилище конфигураций приложения. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры скриптов Azure CLI для службы "Конфигурации приложений" см. в [документации по службе "Конфигурация приложений Azure"](../cli-samples.md).
