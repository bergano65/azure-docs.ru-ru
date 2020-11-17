---
title: Перезапуск сервера-Azure CLI — база данных Azure для PostgreSQL — один сервер
description: В этой статье описывается, как можно перезапустить базу данных Azure для PostgreSQL-Single Server с помощью Azure CLI
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f9881a4517f77587cecb2dcd04befaddb523965b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647653"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Перезапустите базу данных Azure для PostgreSQL — один сервер с помощью Azure CLI
В этой статье объясняется, как перезапустить сервер в Базе данных Azure для PostgreSQL. Возможно, вам потребуется перезапустить сервер в целях обслуживания, что приводит к кратковременному отключению во время выполнения операции.

Если служба занята, перезапустить сервер не удастся. Например, служба может обрабатывать запрошенную ранее операцию, такую как масштабирование виртуальных ядер.
 
Время, необходимое для завершения перезапуска, зависит от процесса восстановления PostgreSQL. Чтобы уменьшить время перезапуска, рекомендуем свести к минимуму объем действий, выполняемых на сервере перед перезапуском.

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим руководством должны быть выполнены следующие условия.
- Создайте [сервер базы данных Azure для PostgreSQL](quickstart-create-server-up-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Для работы с этой статьей требуется версия 2,0 или более поздняя Azure CLI. Если вы используете Azure Cloud Shell, последняя версия уже установлена.

## <a name="restart-the-server"></a>Перезагрузите сервер.

Перезапустите сервер с помощью следующей команды:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [настройке параметров в базе данных Azure для PostgreSQL](howto-configure-server-parameters-using-cli.md)
