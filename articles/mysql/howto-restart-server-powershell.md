---
title: Перезапуск сервера-Azure PowerShell — база данных Azure для MySQL
description: В этой статье описывается, как можно перезапустить сервер базы данных Azure для MySQL с помощью PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/28/2020
ms.openlocfilehash: 935459a398c07d3b4f61c76dec75b083a2354720
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82609062"
---
# <a name="restart-azure-database-for-mysql-server-using-powershell"></a>Перезапуск базы данных Azure для сервера MySQL с помощью PowerShell

В этой статье объясняется, как перезапустить сервер в Базе данных Azure для MySQL. Может потребоваться перезапустить сервер для обслуживания, что приводит к кратковременному отключению во время выполнения операции.

Если служба занята, перезагрузка сервера блокируется. Например, служба может обрабатывать запрошенную ранее операцию, такую как масштабирование виртуальных ядер.

Количество времени, необходимое для выполнения перезапуска, зависит от процесса восстановления MySQL. Чтобы сократить время перезапуска, рекомендуется свести к минимальному объему действий, происходящих на сервере, до перезапуска.

## <a name="prerequisites"></a>Предварительные условия

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

- [Модуль AZ PowerShell](/powershell/azure/install-az-ps) , установленный локально или [Azure Cloud Shell](https://shell.azure.com/) в браузере
- [Сервер базы данных Azure для MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Пока модуль PowerShell AZ. MySql находится на этапе предварительной версии, его необходимо установить отдельно от модуля AZ PowerShell с помощью следующей команды: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Как только модуль PowerShell AZ. MySql станет общедоступным, он становится частью будущих выпусков AZ PowerShell и доступен с помощью встроенных в Azure Cloud Shell.

Если вы решили использовать PowerShell локально, подключитесь к учетной записи Azure с помощью командлета [Connect-азаккаунт](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Перезапустите сервер.

Перезапустите сервер с помощью следующей команды:

```azurepowershell-interactive
Restart-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание базы данных Azure для сервера MySQL с помощью PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md)
