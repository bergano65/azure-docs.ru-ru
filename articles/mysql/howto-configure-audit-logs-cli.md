---
title: Доступ к журналам аудита — Azure CLI — база данных Azure для MySQL
description: В этой статье описывается, как настроить и получить доступ к журналам аудита в базе данных Azure для MySQL из Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: d532e1990586d80d675a8ccb247c0c9f7908bb6f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81384172"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Настройка и доступ к журналам аудита в Azure CLI

Вы можете настроить [журналы аудита базы данных Azure для MySQL](concepts-audit-logs.md) из Azure CLI.

> [!IMPORTANT]
> Функции журнала аудита в настоящее время доступны в предварительной версии.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:

- [Сервер базы данных Azure для MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы проверить версию, в командной строке Azure CLI введите `az --version`. Чтобы выполнить установку или обновление, см. сведения в статье [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Настройка ведения журнала аудита

Включите и настройте ведение журнала аудита, выполнив следующие действия.

1. Включите журналы аудита, установив для параметра **audit_logs_enabled** значение ON. 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Выберите [типы событий](concepts-audit-logs.md#configure-audit-logging) для записи, обновив параметр **audit_log_events** .
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Добавьте пользователей MySQL, которые будут исключены из ведения журнала, путем обновления параметра **audit_log_exclude_users** . Укажите пользователей, указав имя пользователя MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Добавьте конкретных пользователей MySQL, которые будут включаться в ведение журнала, обновив параметр **audit_log_include_users** . Укажите пользователей, указав имя пользователя MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения о [журналах аудита](concepts-audit-logs.md) в базе данных Azure для MySQL
- Узнайте, как настроить журналы аудита в [портал Azure](howto-configure-audit-logs-portal.md)