---
title: Автоматическое хранение данных - Azure CLI - База данных Azure для PostgreS'L - Единый сервер
description: В этой статье описывается, как можно настроить автоматическое увеличение хранилища с помощью Azure CLI в базе данных Azure для PostgreS-L - Single Server.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 8/7/2019
ms.openlocfilehash: b0dc2fbb168d9325439ee18a227f71a3b88ef9c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74767965"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>База данных Azure автоматического выращивания для хранения PostgreS-L - Единый сервер с помощью Azure CLI
В этой статье описывается, как можно настроить базу данных Azure для хранения серверов PostgreS'L для роста, не влияя на рабочую нагрузку.

Сервер, [достигающий предела хранилища,](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit)настроен только на чтение. Если автоматическое увеличение хранилища включено, то для серверов с менее чем 100 ГБ подготовленного хранилища, объем резервированного хранилища увеличивается на 5 ГБ, как только свободное хранилище ниже больше 1 ГБ или 10% от предусмотренного хранилища. Для серверов с более чем 100 ГБ подготовленного хранилища размер резервированного хранилища увеличивается на 5%, когда свободное пространство для хранения ниже 5% от предусмотренного размера хранилища. Максимальные лимиты хранения, указанные [здесь,](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) применяются.

## <a name="prerequisites"></a>Предварительные требования
Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:
- [сервер базы данных Azure для PostgreSQL](quickstart-create-server-database-azure-cli.md);

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы проверить версию, в командной строке Azure CLI введите `az --version`. Чтобы выполнить установку или обновление, см. сведения в статье [Установка Azure CLI]( /cli/azure/install-azure-cli).

## <a name="enable-postgresql-server-storage-auto-grow"></a>Включить автоматическое хранение серверов PostgreS'L

Включить автоматическое хранилище сервера на существующем сервере со следующей командой:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Включить автоматическое хранилище сервера при создании нового сервера со следующей командой:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о [том, как создавать оповещения по метрикам.](howto-alert-on-metric.md)