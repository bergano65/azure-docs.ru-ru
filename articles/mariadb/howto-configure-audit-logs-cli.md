---
title: Доступ к журналам аудита — Azure CLI — база данных Azure для MariaDB
description: В этой статье описывается, как настроить и получить доступ к журналам аудита в базе данных Azure для MariaDB из Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: e9716f0fa8e0ae44d614bbb28ed6846105e683d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81384198"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Настройка и доступ к журналам аудита в Azure CLI

Вы можете настроить [журналы аудита базы данных Azure для MariaDB](concepts-audit-logs.md) из Azure CLI.

> [!IMPORTANT]
> Функции журнала аудита в настоящее время доступны в предварительной версии.

## <a name="prerequisites"></a>Предварительные условия

Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:

- [База данных Azure для сервера MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы проверить версию, в командной строке Azure CLI введите `az --version`. Чтобы выполнить установку или обновление, см. сведения в статье [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Настройка ведения журнала аудита

Включите и настройте ведение журнала аудита, выполнив следующие действия. 

1. Включите журналы аудита, установив для параметра **audit_logs_enabled** значение ON. 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Выберите [типы событий](concepts-audit-logs.md#configure-audit-logging) для записи, обновив параметр **audit_log_egitvents** .
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Добавьте всех пользователей MariaDB, которые будут исключены из ведения журнала, путем обновления параметра **audit_log_exclude_users** . Укажите пользователей, указав имя пользователя MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Добавьте всех конкретных пользователей MariaDB, включаемых для ведения журнала, путем обновления параметра **audit_log_include_users** . Укажите пользователей, указав имя пользователя MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о [журналах аудита](concepts-audit-logs.md) в базе данных Azure для MariaDB
- Узнайте, как настроить журналы аудита в [портал Azure](howto-configure-audit-logs-portal.md)