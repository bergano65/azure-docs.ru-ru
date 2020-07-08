---
title: Настройка параметров сервера — Azure PowerShell — база данных Azure для MariaDB
description: В этой статье описывается, как настроить параметры службы в базе данных Azure для MariaDB с помощью PowerShell.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: conceptual
ms.date: 5/26/2020
ms.openlocfilehash: 53ef9f66034d40e221a9ae4c8647cecdfe709107
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84726894"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-powershell"></a>Настройка параметров сервера в базе данных Azure для MariaDB с помощью PowerShell

Вы можете вывести список, отобразить и обновить параметры конфигурации для сервера базы данных Azure для MariaDB с помощью PowerShell. Только подмножество конфигураций ядра предоставляется на уровне сервера и может быть изменено.

## <a name="prerequisites"></a>Предварительные требования

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

- [Модуль AZ PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) , установленный локально или [Azure Cloud Shell](https://shell.azure.com/) в браузере
- [Сервер базы данных Azure для MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Так как модуль PowerShell Az.MariaDb предоставляется в предварительной версии, его нужно установить отдельно от модуля Az PowerShell с помощью команды `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Как только модуль PowerShell Az.MariaDb станет общедоступным, он будет включен в один из будущих выпусков модуля Az PowerShell и встроен в Azure Cloud Shell.

Если вы решили использовать PowerShell локально, подключитесь к учетной записи Azure с помощью командлета [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Получение списка параметров конфигурации сервера Базы данных Azure для MariaDB

Чтобы перечислить все изменяемые параметры на сервере и их значения, выполните `Get-AzMariaDbConfiguration` командлет.

В следующем примере перечисляются параметры конфигурации сервера **mydemoserver** в группе ресурсов **myresourcegroup**.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Определение каждого из перечисленных параметров см. в разделе ссылок MariaDB на странице [Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html) (Системные переменные сервера).

## <a name="show-server-configuration-parameter-details"></a>Отображение сведений о параметре конфигурации сервера

Чтобы отобразить сведения о конкретном параметре конфигурации для сервера, выполните `Get-AzMariaDbConfiguration` командлет и укажите параметр **Name** .

В этом примере показаны подробные сведения о параметре конфигурации сервера ** \_ \_ log** **mydemoserver** для сервера в разделе **myresourcegroup**группы ресурсов.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Изменение значения параметра конфигурации сервера

Вы также можете изменить значение определенного параметра конфигурации сервера. При этом обновляется базовое значение конфигурации для ядра СУБД сервера MariaDB. Чтобы обновить конфигурацию, используйте `Update-AzMariaDbConfiguration` командлет.

Чтобы обновить параметр конфигурации сервера ** \_ \_ log** **mydemoserver** для сервера в разделе **myresourcegroup**группы ресурсов.

```azurepowershell-interactive
Update-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Автоматическое увеличение размера хранилища в базе данных Azure для MariaDB с помощью PowerShell](howto-auto-grow-storage-powershell.md).
