---
title: Перезапуск сервера-Azure PowerShell — база данных Azure для MariaDB
description: В этой статье описывается, как можно перезапустить сервер базы данных Azure для MariaDB с помощью PowerShell.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0311111285d7dc0d60bc63ce9cef2be3f0ddfb19
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664874"
---
# <a name="restart-azure-database-for-mariadb-server-using-powershell"></a>Перезапуск базы данных Azure для сервера MariaDB с помощью PowerShell

В этой статье объясняется, как перезапустить сервер Базы данных Azure для MariaDB. Может потребоваться перезапустить сервер для обслуживания, что приводит к кратковременному отключению во время выполнения операции.

Если служба занята, перезагрузка сервера блокируется. Например, служба может обрабатывать запрошенную ранее операцию, такую как масштабирование виртуальных ядер.

Количество времени, необходимое для выполнения перезагрузки, зависит от процесса восстановления MariaDB. Чтобы сократить время перезапуска, рекомендуется свести к минимальному объему действий, происходящих на сервере, до перезапуска.

## <a name="prerequisites"></a>Предварительные условия

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

- [Модуль AZ PowerShell](/powershell/azure/install-az-ps) , установленный локально или [Azure Cloud Shell](https://shell.azure.com/) в браузере
- [Сервер базы данных Azure для MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Так как модуль PowerShell Az.MariaDb предоставляется в предварительной версии, его нужно установить отдельно от модуля Az PowerShell с помощью команды `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Как только модуль PowerShell Az.MariaDb станет общедоступным, он будет включен в один из будущих выпусков модуля Az PowerShell и встроен в Azure Cloud Shell.

Если вы решили использовать PowerShell локально, подключитесь к учетной записи Azure с помощью командлета [Connect-азаккаунт](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Перезагрузите сервер.

Перезапустите сервер с помощью следующей команды:

```azurepowershell-interactive
Restart-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Создание сервера базы данных Azure для MariaDB с помощью PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)