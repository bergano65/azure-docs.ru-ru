---
title: Автоматическое хранение - Azure CLI - База данных Azure для MyS'L
description: В этой статье описывается, как можно включить автоматическое хранение данных с использованием Azure CLI в базе данных Azure для MyS'L.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 44ce852aaf2ed5839650132c6eae95728c27dc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062633"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>База данных Azure автоматического выращивания для хранения MyS'L с помощью Azure CLI
В этой статье описывается, как можно настроить базу данных Azure для хранения серверов MyS'L для роста, не влияя на рабочую нагрузку.

Сервер, [достигающий предела хранилища,](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit)настроен только на чтение. Если автоматическое увеличение хранилища включено, то для серверов с менее чем 100 ГБ подготовленного хранилища, объем резервированного хранилища увеличивается на 5 ГБ, как только свободное хранилище ниже больше 1 ГБ или 10% от предусмотренного хранилища. Для серверов с более чем 100 ГБ подготовленного хранилища размер резервированного хранилища увеличивается на 5%, когда свободное пространство для хранения ниже 5% от предусмотренного размера хранилища. Максимальные лимиты хранения, указанные [здесь,](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) применяются.

## <a name="prerequisites"></a>Предварительные требования
Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:
- [База данных Azure для сервера MyS'L](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы проверить версию, в командной строке Azure CLI введите `az --version`. Чтобы выполнить установку или обновление, см. сведения в статье [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="enable-mysql-server-storage-auto-grow"></a>Включить автоматическое хранение серверов MyS'L

Включить автоматическое хранилище сервера на существующем сервере со следующей командой:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Включить автоматическое хранилище сервера при создании нового сервера со следующей командой:

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о [том, как создавать оповещения по метрикам.](howto-alert-on-metric.md)