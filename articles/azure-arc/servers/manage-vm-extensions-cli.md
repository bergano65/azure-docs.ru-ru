---
title: Включение расширения виртуальной машины с помощью Azure CLI
description: В этой статье описывается, как развернуть расширения виртуальной машины на серверах с поддержкой дуги Azure, работающих в гибридных облачных средах, с помощью Azure CLI.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 8f09914f246635f07b3c51c682bd67591c706732
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92463007"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Включение расширений виртуальной машины Azure с помощью Azure CLI

В этой статье показано, как развернуть и удалить расширения виртуальной машины Azure, поддерживаемые серверами с поддержкой ARC в Azure, на гибридный компьютер под управлением Linux или Windows с помощью Azure CLI.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="prerequisites"></a>Предварительные требования

[Установка Azure CLI](/cli/azure/install-azure-cli).

Прежде чем использовать Azure CLI для управления расширениями виртуальной машины на гибридном сервере, управляемом серверами с поддержкой Arc, необходимо установить `ConnectedMachine` расширение CLI. Выполните следующую команду на сервере с поддержкой ARC:

```azurecli
az extension add connectedmachine
```

После завершения установки возвращается следующее сообщение:

`The installed extension `connectedmachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Включить расширение

Чтобы включить расширение виртуальной машины на сервере с поддержкой Arc, выполните команду [AZ коннектедмачине Machine-Extension Create](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_create) с `--machine-name` параметрами,, `--extension-name` ,, `--location` `--type` `settings` и `--publisher` .

В следующем примере показано включение расширения виртуальной машины Log Analytics на сервере Linux с поддержкой ARC:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "OmsAgentforLinux" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.EnterpriseCloud.Monitoring" --settings "{\"workspaceId\":\"workspaceId"}" --protected-settings "{\workspaceKey\":"\workspaceKey"} --type-handler-version "1.10" --resource-group "myResourceGroup"
```

В следующем примере активируется расширение пользовательского скрипта на сервере с поддержкой ARC:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

## <a name="list-extensions-installed"></a>Список установленных расширений

Чтобы получить список расширений виртуальной машины на сервере с поддержкой Arc, выполните команду [AZ коннектедмачине Machine-Extension List](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_list) с `--machine-name` `--resource-group` параметрами и.

Пример

```azurecli
az connectedmachine machine-extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

По умолчанию выходные данные команд Azure CLI в формате JSON (JavaScript Object Notation). Например, чтобы изменить назначение выходных данных на таблицу или список, используйте команду [az configure --output](/cli/azure/reference-index). Параметр `--output` можно добавлять к любой команде — он один раз изменяет формат выходных данных.

В следующем примере показаны частичные выходные данные JSON из `az connectedmachine machine-extension -list` команды:

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>Удаление установленного расширения

Чтобы удалить установленное расширение виртуальной машины на сервере с поддержкой Arc, выполните команду [AZ коннектедмачине Machine-Extension Delete](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_delete) с `--extension-name` `--machine-name` `--resource-group` параметрами и.

Например, чтобы удалить расширение виртуальной машины Log Analytics для Linux, выполните следующую команду:

```azurecli
az connectedmachine machine-extension delete --machine-name "myMachineName" --name "OmsAgentforLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Дальнейшие действия

- Вы можете развертывать, администрировать и удалять расширения ВМ с помощью [PowerShell](manage-vm-extensions-powershell.md), из [портал Azure](manage-vm-extensions-portal.md)или [Azure Resource Manager шаблонов](manage-vm-extensions-template.md).

- Сведения об устранении неполадок можно найти в разделе [руководство по устранению неполадок РАСШИРЕНИЙ ВМ](troubleshoot-vm-extensions.md).
