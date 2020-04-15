---
title: Журналы аудита доступа - Azure CLI - База данных Azure для MyS'L
description: В этой статье описывается, как настроить и получить доступ к журналам аудита в базе данных Azure для MyS'L из Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: d532e1990586d80d675a8ccb247c0c9f7908bb6f
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384172"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Настройка и доступ к журналам аудита в Azure CLI

Вы можете настроить [базу данных Azure для журналов аудита MyS'L](concepts-audit-logs.md) из Azure CLI.

> [!IMPORTANT]
> Функциональность журнала аудита в настоящее время находится в предварительном просмотре.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:

- [База данных Azure для сервера MyS'L](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы проверить версию, в командной строке Azure CLI введите `az --version`. Чтобы выполнить установку или обновление, см. сведения в статье [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Настройка журналов аудита

Включить и настроить журнал аудита с использованием следующих шагов:

1. Включите журналы аудита, установив параметр **audit_logs_enabled** на "ON". 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Выберите [типы событий,](concepts-audit-logs.md#configure-audit-logging) которые должны быть зарегистрированы путем обновления **параметра audit_log_events.**
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Добавьте всех пользователей MyS'L, которые **audit_log_exclude_users** будут исключены из журнала, обновив audit_log_exclude_users параметр. Укажите пользователям, предоставив их имя пользователя MyS'L.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Добавьте для входа в журнал для входа определенные пользователи MyS'L, обновив **параметр audit_log_include_users.** Укажите пользователям, предоставив их имя пользователя MyS'L.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Следующие шаги
- Узнайте больше о [журналах аудита](concepts-audit-logs.md) в базе данных Azure для MyS'L
- Узнайте, как настроить журналы аудита на [портале Azure](howto-configure-audit-logs-portal.md)