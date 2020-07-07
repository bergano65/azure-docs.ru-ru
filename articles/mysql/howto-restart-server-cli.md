---
title: Перезапуск сервера-Azure CLI — база данных Azure для MySQL
description: В этой статье описывается, как можно перезапустить сервер базы данных Azure для MySQL с помощью Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c1fd688fbfd892e3d0dfc3ebb1712dd931e0ed39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80063503"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Перезапустите базу данных Azure для сервера MySQL с помощью Azure CLI
В этой статье объясняется, как перезапустить сервер в Базе данных Azure для MySQL. Возможно, вам потребуется перезапустить сервер в целях обслуживания, что приводит к кратковременному отключению во время выполнения операции.

Если служба занята, перезапустить сервер не удастся. Например, служба может обрабатывать запрошенную ранее операцию, такую как масштабирование виртуальных ядер.

Время, необходимое для завершения перезапуска, зависит от процесса восстановления MySQL. Чтобы уменьшить время перезапуска, рекомендуем свести к минимуму объем действий, выполняемых на сервере перед перезапуском.

## <a name="prerequisites"></a>Предварительные требования
Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:
- [Сервер базы данных Azure для MySQL](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы проверить версию, в командной строке Azure CLI введите `az --version`. Чтобы выполнить установку или обновление, см. сведения в статье [Установка Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Перезагрузите сервер.

Перезапустите сервер с помощью следующей команды:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [настройке параметров в базе данных Azure для MySQL](howto-configure-server-parameters-using-cli.md)