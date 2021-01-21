---
title: Автоматическое расширение хранилища — Azure PowerShell — база данных Azure для MariaDB
description: В этой статье описывается, как включить автоматическое расширение хранилища с помощью PowerShell в базе данных Azure для MariaDB.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ef495fc884b1930f8510b6b2443bde40e269a8f1
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98665163"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-server-using-powershell"></a>Автоматическое увеличение объема хранилища в базе данных Azure для сервера MariaDB с помощью PowerShell

В этой статье описывается, как можно настроить расширение хранилища базы данных Azure для MariaDB, не влияя на рабочую нагрузку.

Автоматическое увеличение хранилища предотвращает [достижение сервером ограничения хранилища](concepts-pricing-tiers.md#reaching-the-storage-limit) и становится только чтением. Для серверов с 100 ГБ или меньше подготовленного хранилища размер увеличивается на 5 ГБ, когда объем свободного пространства ниже 10%. Для серверов с более чем 100 ГБ подготовленного хранилища размер увеличивается на 5%, если объем свободного пространства меньше 10 ГБ. Максимальные ограничения хранилища применяются, как указано в разделе Storage ценовых категорий [базы данных Azure для MariaDB](concepts-pricing-tiers.md#storage).

> [!IMPORTANT]
> Помните, что хранилище можно масштабировать только вверх, а не вниз.

## <a name="prerequisites"></a>Предварительные условия

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

- [Модуль AZ PowerShell](/powershell/azure/install-az-ps) , установленный локально или [Azure Cloud Shell](https://shell.azure.com/) в браузере
- [Сервер базы данных Azure для MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Так как модуль PowerShell Az.MariaDb предоставляется в предварительной версии, его нужно установить отдельно от модуля Az PowerShell с помощью команды `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Как только модуль PowerShell Az.MariaDb станет общедоступным, он будет включен в один из будущих выпусков модуля Az PowerShell и встроен в Azure Cloud Shell.

Если вы решили использовать PowerShell локально, подключитесь к учетной записи Azure с помощью командлета [Connect-азаккаунт](/powershell/module/az.accounts/connect-azaccount) .

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

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Как создавать реплики чтения и управлять ими в базе данных Azure для MariaDB с помощью PowerShell](howto-read-replicas-powershell.md).
