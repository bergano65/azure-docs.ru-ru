---
title: Настройка параметров сервера — Azure PowerShell — база данных Azure для MySQL
description: В этой статье описывается, как настроить параметры службы в базе данных Azure для MySQL с помощью PowerShell.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: conceptual
ms.date: 4/29/2020
ms.openlocfilehash: 6c91f1494e56e9176f8bbf79387144aea5942787
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84725194"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-powershell"></a>Настройка параметров сервера в базе данных Azure для MySQL с помощью PowerShell

Вы можете вывести список, отобразить и обновить параметры конфигурации для сервера базы данных Azure для MySQL с помощью PowerShell. Только подмножество конфигураций ядра предоставляется на уровне сервера и может быть изменено.

## <a name="prerequisites"></a>Предварительные требования

Вот что вам нужно, чтобы выполнить инструкции, приведенные в этом руководстве:

- [Модуль AZ PowerShell](/powershell/azure/install-az-ps) , установленный локально или [Azure Cloud Shell](https://shell.azure.com/) в браузере
- [Сервер базы данных Azure для MySQL](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Так как модуль PowerShell Az.MySql предоставляется в режиме предварительной версии, его нужно установить отдельно от модуля Az с помощью команды `Install-Module -Name Az.MySql -AllowPrerelease`.
> Как только модуль PowerShell Az.MySql станет общедоступным, он будет включен в один из будущих выпусков Az PowerShell и встроен в Azure Cloud Shell.

Если вы решили использовать PowerShell локально, подключитесь к учетной записи Azure с помощью командлета [Connect-азаккаунт](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Получение списка параметров конфигурации сервера для базы данных Azure для сервера MySQL

Чтобы перечислить все изменяемые параметры на сервере и их значения, выполните `Get-AzMySqlConfiguration` командлет.

В следующем примере перечисляются параметры конфигурации сервера **mydemoserver** в группе ресурсов **myresourcegroup**.

```azurepowershell-interactive
Get-AzMySqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Определение каждого из перечисленных параметров см. в разделе ссылок MySQL на странице[Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html) (Системные переменные сервера).

## <a name="show-server-configuration-parameter-details"></a>Отображение сведений о параметре конфигурации сервера

Чтобы отобразить сведения о конкретном параметре конфигурации для сервера, выполните `Get-AzMySqlConfiguration` командлет и укажите параметр **Name** .

В этом примере показаны подробные сведения о параметре конфигурации сервера ** \_ \_ log** **mydemoserver** для сервера в разделе **myresourcegroup**группы ресурсов.

```azurepowershell-interactive
Get-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Изменение значения параметра конфигурации сервера

Вы также можете изменить значение определенного параметра конфигурации сервера. При этом обновляется базовое значение конфигурации для ядра СУБД сервера MySQL. Чтобы обновить конфигурацию, используйте `Update-AzMySqlConfiguration` командлет.

Чтобы обновить параметр конфигурации сервера ** \_ \_ log** **mydemoserver** для сервера в разделе **myresourcegroup**группы ресурсов.

```azurepowershell-interactive
Update-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Автоматическое увеличение объема хранилища в базе данных Azure для сервера MySQL с помощью PowerShell](howto-auto-grow-storage-powershell.md).
