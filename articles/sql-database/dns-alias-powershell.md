---
title: PowerShell для псевдонима DNS
description: Смлеты PowerShell, такие как New-AzSqlServerDNSAlias, позволяют перенаправлять новые клиентские соединения на другой сервер базы данных Azure S'L, не прикасаясь к любой клиентской конфигурации.
keywords: dns sql database
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 9232a99ddd29201e6743c09455d79e9ba22b3b9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420404"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>Управление псевдонимом DNS для Базы данных SQL Azure с помощью PowerShell

В этой статье представлен скрипт PowerShell, на примере которого показано, как управлять псевдонимом DNS для Базы данных SQL Azure.

> [!NOTE]
> Эта статья была обновлена для использования либо модуля Azure PowerShell Az, либо Azure CLI. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г.
>
> Подробнее о модуле Az и совместимости AzureRM читайте в [представлении модуля Azure PowerShell Az.](/powershell/azure/new-azureps-module-az) Для инструкций по установке смотрите [Установить Azure PowerShell](/powershell/azure/install-az-ps) или [установить Azure CLI.](/cli/azure/install-azure-cli)

## <a name="dns-alias-in-connection-string"></a>Использование псевдонима DNS в строке подключения

Чтобы подключить определенный сервер Базы данных SQL Azure, клиент (например, SQL Server Management Studio) может указать псевдоним DNS вместо действительного имени сервера. В следующем примере псевдоним *any-unique-alias-name* заменяет имя первого узла с разделителями-точками в строке сервера с четырьмя узлами.

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить пример скрипта PowerShell, приведенный в этой статье, потребуются следующие компоненты:

- Подписка и учетная запись Azure для бесплатной [пробной версии](https://azure.microsoft.com/free/) см.
- Два сервера баз данных Azure S'L

## <a name="example"></a>Пример

Следующий пример кода начинается с присвоения буквальных значений нескольким переменным.

Чтобы запустить код, отспособьте значения заполнителя в соответствии с реальными значениями в вашей системе.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Используемые cmdlets:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Создает псевдоним DNS в системе обслуживания базы данных Azure S'L. Псевдоним относится к серверу базы данных 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Получить и перечислить все псевдонимы, назначенные на сервере S'L DB 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): изменяет имя сервера, на которое настроен псевдоним, от сервера 1 до сервера 2.
- [Удалить-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Удалить псевдоним с сервера базы данных 2, используя имя псевдонима.

Чтобы выполнить установку или обновление, см. статью [Установка и настройка Azure PowerShell](/powershell/azure/install-az-ps).

Используйте `Get-Module -ListAvailable Az` в *powershell\_ise.exe*, чтобы найти версию.

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Используемые команды следующие:

- [az sql сервер dns-псевдоним ы создают:](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias)Создает псевдоним DNS в системе обслуживания базы данных Azure S'L. Псевдоним относится к серверу базы данных 1.
- [az sql сервер dns-псевдонимы показывают:](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias)Получить и перечислить все псевдонимы, назначенные на сервер е-L DB 1.
- [az sql server dns-alias set](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Модифицирует имя сервера, на которое настроен псевдоним, от сервера 1 до сервера 2.
- [az sql сервер dns-псевдоним удалить](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Удалить псевдоним из базы данных сервера 2, используя имя псевдонима.

Чтобы выполнить установку или обновление, см. сведения в статье [Установка Azure CLI](/cli/azure/install-azure-cli).

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>Дальнейшие действия

Полное объяснение функции dNS-псевдонима для базы данных S'L можно просмотреть [псевдоним DNS для базы данных Azure S'L.](dns-alias-overview.md)
