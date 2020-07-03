---
title: Пример для CLI. Создание управляемого экземпляра в Базе данных SQL Azure
description: Пример скрипта Azure CLI для создания управляемого экземпляра в Базе данных SQL Azure
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: f52a460be33b23da3da0cb32075ab5f75bb8d129
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772630"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>Создание управляемого экземпляра Базы данных SQL Azure с помощью CLI

Этот пример скрипта Azure CLI создает управляемый экземпляр Базы данных SQL Azure в выделенной подсети в новой виртуальной сети. Он также настраивает таблицу маршрутов и группу безопасности сети для виртуальной сети. После успешного выполнения сценария доступ к управляемому экземпляру можно получить из виртуальной сети или из локальной среды. См. стать. о [настройке виртуальной машины Azure для подключения к Управляемому экземпляру Базы данных SQL Azure](../sql-database-managed-instance-configure-vm.md), а также стать. о [настройке подключения "точка — сеть" к Управляемому экземпляру Базы данных SQL Azure из локальной среды](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Сведения об ограничениях см. в разделах [Поддерживаемые регионы](../sql-database-managed-instance-resource-limits.md#supported-regions) и [Поддерживаемые типы подписок](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Пример скрипта

### <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Выполнение скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Очистка развертывания

Используйте приведенную ниже команду, чтобы удалить группу ресурсов и все связанные с ней ресурсы.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Примеры

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| | |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | Команды виртуальной сети. |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | Команды подсети виртуальной сети. |
| [az network route-table](/cli/azure/network/route-table) | Команды таблицы маршрутизации. |
| [az sql mi](/cli/azure/sql/mi) | Команды управляемого экземпляра. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры сценариев интерфейса командной строки для Базы данных SQL Azure см. в [документации по Базе данных SQL](../sql-database-cli-samples.md).
