---
title: Автоматическое расширение хранилища — Azure PowerShell — база данных Azure для MariaDB
description: В этой статье описывается, как включить автоматическое расширение хранилища с помощью PowerShell в базе данных Azure для MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2d03a67fc8a8172573598662ad9770b28493e9a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87497109"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-server-using-powershell"></a>Автоматическое увеличение объема хранилища в базе данных Azure для сервера MariaDB с помощью PowerShell

В этой статье описывается, как можно настроить расширение хранилища базы данных Azure для MariaDB, не влияя на рабочую нагрузку.

Автоматическое увеличение хранилища предотвращает [достижение сервером ограничения хранилища](/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit) и становится только чтением. Для серверов с 100 ГБ или меньше подготовленного хранилища размер увеличивается на 5 ГБ, когда объем свободного пространства ниже 10%. Для серверов с более чем 100 ГБ подготовленного хранилища размер увеличивается на 5%, если объем свободного пространства меньше 10 ГБ. Максимальные ограничения хранилища применяются, как указано в разделе Storage ценовых категорий [базы данных Azure для MariaDB](/azure/mariadb/concepts-pricing-tiers#storage).

> [!IMPORTANT]
> Помните, что хранилище можно масштабировать только вверх, а не вниз.

## <a name="prerequisites"></a>Предварительные требования

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

- [Модуль AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) , установленный локально или [Azure Cloud Shell](https://shell.azure.com/) в браузере
- [Сервер базы данных Azure для MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Так как модуль PowerShell Az.MariaDb предоставляется в предварительной версии, его нужно установить отдельно от модуля Az PowerShell с помощью команды `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Как только модуль PowerShell Az.MariaDb станет общедоступным, он будет включен в один из будущих выпусков модуля Az PowerShell и встроен в Azure Cloud Shell.

Если вы решили использовать PowerShell локально, подключитесь к учетной записи Azure с помощью командлета [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

## <a name="enable-mariadb-server-storage-auto-grow"></a>Включить автоматическое увеличение размера хранилища сервера MariaDB

Включите автоматическое расширение хранилища на сервере на существующем сервере с помощью следующей команды:

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Включите автоматическое расширение хранилища сервера при создании нового сервера с помощью следующей команды:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Как создавать реплики чтения и управлять ими в базе данных Azure для MariaDB с помощью PowerShell](howto-read-replicas-powershell.md).
