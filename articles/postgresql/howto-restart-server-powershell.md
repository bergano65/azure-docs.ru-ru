---
title: Перезапуск сервера-Azure PowerShell — база данных Azure для PostgreSQL
description: В этой статье описывается, как можно перезапустить сервер базы данных Azure для PostgreSQL с помощью PowerShell.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.openlocfilehash: 95a0459dd21eaf69bf0ee2d8f4cd1482d07313bb
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107930"
---
# <a name="restart-azure-database-for-postgresql-server-using-powershell"></a>Перезапуск базы данных Azure для сервера PostgreSQL с помощью PowerShell

В этой статье объясняется, как перезапустить сервер в Базе данных Azure для PostgreSQL. Может потребоваться перезапустить сервер для обслуживания, что приводит к кратковременному отключению во время выполнения операции.

Если служба занята, перезагрузка сервера блокируется. Например, служба может обрабатывать запрошенную ранее операцию, такую как масштабирование виртуальных ядер.

Количество времени, необходимое для выполнения перезагрузки, зависит от процесса восстановления PostgreSQL. Чтобы сократить время перезапуска, рекомендуется свести к минимальному объему действий, происходящих на сервере, до перезапуска.

## <a name="prerequisites"></a>Предварительные требования

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

- [Модуль AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) , установленный локально или [Azure Cloud Shell](https://shell.azure.com/) в браузере
- [сервер базы данных Azure для PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md);

> [!IMPORTANT]
> Так как модуль Az.PostgreSql PowerShell предоставляется в режиме предварительной версии, его нужно установить отдельно от модуля Az с помощью команды `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Как только модуль Az.PostgreSql PowerShell станет общедоступным, он будет включен в один из будущих выпусков Az PowerShell и встроен в Azure Cloud Shell.

Если вы решили использовать PowerShell локально, подключитесь к учетной записи Azure с помощью командлета [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Перезагрузите сервер.

Перезапустите сервер с помощью следующей команды:

```azurepowershell-interactive
Restart-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание сервера базы данных Azure для PostgreSQL с помощью PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md)
