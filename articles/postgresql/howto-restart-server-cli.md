---
title: Перезагрузка сервера - Azure CLI - База данных Azure для PostgreS'L - Единый сервер
description: В этой статье описывается, как можно перезапустить базу данных Azure для PostgreS-L - Единый сервер с помощью Azure CLI
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 13b26b545f9e95ce2457e4f8d9cf32da59cd91e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770158"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Перезагрузка базы данных Azure для PostgreS'L - Единый сервер с помощью Azure CLI
В этой статье объясняется, как перезапустить сервер в Базе данных Azure для PostgreSQL. Возможно, вам потребуется перезапустить сервер в целях обслуживания, что приводит к кратковременному отключению во время выполнения операции.

Если служба занята, перезапустить сервер не удастся. Например, служба может обрабатывать запрошенную ранее операцию, такую как масштабирование виртуальных ядер.
 
Время, необходимое для завершения перезапуска, зависит от процесса восстановления PostgreSQL. Чтобы уменьшить время перезапуска, рекомендуем свести к минимуму объем действий, выполняемых на сервере перед перезапуском.

## <a name="prerequisites"></a>Предварительные требования
Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:
- [сервер базы данных Azure для PostgreSQL](quickstart-create-server-up-azure-cli.md);

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы проверить версию, в командной строке Azure CLI введите `az --version`. Чтобы выполнить установку или обновление, см. сведения в статье [Установка Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Перезапустите сервер.

Перезазапуск сервера со следующей командой:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о [том, как устанавливать параметры в базе данных Azure для PostgreS'L](howto-configure-server-parameters-using-cli.md)