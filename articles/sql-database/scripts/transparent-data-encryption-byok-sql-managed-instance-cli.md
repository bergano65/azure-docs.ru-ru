---
title: Пример CLI. Включение BYOK TDE-Управляемый экземпляр Базы данных SQL Azure
description: Узнайте, как настроить для Управляемого экземпляра Базы данных SQL BYOK TDE для прозрачного шифрования неактивных данных с помощью PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: 8e8c0e2db1f87cca52c44d33ce14d7ce4f00e895
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061739"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Управление прозрачным шифрованием данных в Управляемом экземпляре с использованием собственного ключа из Azure Key Vault

В этом примере скрипта Azure CLI настраивается прозрачное шифрование данных (TDE) с ключом, управляемым клиентом, для Управляемый экземпляр Azure SQL с помощью ключа из Azure Key Vault. Часто это называется создание собственных ключейным сценарием для TDE. Дополнительные сведения о TDE с ключом, управляемым клиентом, см. в разделе [TDE создание собственных ключей to Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Пример скрипта

### <a name="prerequisites"></a>Предварительные условия

Существующий Управляемый экземпляр см. [в разделе использование Azure CLI для создания управляемого экземпляра базы данных SQL Azure](sql-database-create-configure-managed-instance-cli.md).

### <a name="sign-in-to-azure"></a>Вход в Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Выполнение скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>Очистка развертывания

Используйте следующую команду, чтобы удалить группу ресурсов и все связанные с ней ресурсы.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Образец ссылки

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| | |
|---|---|
| [AZ SQL DB](/cli/azure/sql/db) | Команды базы данных. |
| [AZ SQL Failover — группа](/cli/azure/sql/failover-group) | Команды группы отработки отказа. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры сценариев интерфейса командной строки для Базы данных SQL Azure см. в [документации по Базе данных SQL](../sql-database-cli-samples.md).
