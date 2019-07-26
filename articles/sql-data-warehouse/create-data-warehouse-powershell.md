---
title: Краткое руководство. Создание Хранилища данных SQL Azure с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как быстро создать логический сервер базы данных SQL, правило брандмауэра на уровне сервера и хранилище данных с помощью Azure PowerShell.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 1f800ade5c5122f0891c9122f6698b6550048c67
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479339"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Краткое руководство. Создание Хранилища данных SQL Azure и его запрос с помощью Azure PowerShell

С помощью Azure PowerShell можно быстро создать хранилище данных SQL Azure.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

> [!NOTE]
> Создание хранилища данных SQL может привести к дополнительным расходам.  Дополнительные сведения см. на странице [цен на хранилище данных SQL](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Вход в Azure

С помощью команды [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) войдите в подписку Azure и следуйте инструкциям на экране.

```powershell
Connect-AzAccount
```

Чтобы узнать, какие подписки вы используете, выполните [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Если необходимо использовать подписку не по умолчанию, выполните [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Создание переменных

Определите переменные для использования в скриптах этого краткого руководства.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов Azure ](../azure-resource-manager/resource-group-overview.md) с помощью команды [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа. В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Создание логического сервера

Создайте [логический сервер SQL Azure](../sql-database/sql-database-logical-servers.md) с помощью команды [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver). Логический сервер содержит группу баз данных, которыми можно управлять как группой. В примере ниже показано создание сервера со случайным именем в группе ресурсов с именем администратора `ServerAdmin` и паролем `ChangeYourAdminPassword1`. Замените эти предопределенные значения по своему усмотрению.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Настройка правил брандмауэра сервера

Создайте [правило брандмауэра на уровне сервера SQL Azure](../sql-database/sql-database-firewall-configure.md) с помощью команды [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule). Правило брандмауэра на уровне сервера позволяет внешним приложениям, таким как SQL Server Management Studio или программе sqlcmd, подключаться к хранилищу данных SQL через брандмауэр службы хранилища данных SQL. В следующем примере брандмауэр открыт только для других ресурсов Azure. Чтобы включить возможность внешнего подключения, измените IP-адрес на соответствующий адрес своей среды. Чтобы открыть все IP-адреса, используйте 0.0.0.0 как начальный IP-адрес, а 255.255.255.255 — как конечный.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> База данных SQL и хранилище данных SQL взаимодействуют через порт 1433. Если вы пытаетесь подключиться из корпоративной сети, исходящий трафик через порт 1433 может быть запрещен сетевым брандмауэром. В таком случае вы не сможете подключиться к серверу SQL Azure. Для этого ваш ИТ-отдел должен открыть порт 1433.
>


## <a name="create-a-data-warehouse"></a>Создание хранилища данных
В этом примере создается хранилище данных с использованием ранее определенных переменных.  Он определяет цель службы как DW100c, что является бюджетной начальной точкой для хранилища данных. 

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Ниже перечислены необходимые параметры.

* **RequestedServiceObjectiveName**: количество запрашиваемых [единиц хранилища данных](what-is-a-data-warehouse-unit-dwu-cdwu.md). Увеличение этого количества приведет к повышению стоимости вычислений. Список поддерживаемых значений см. в статье [Ограничения параллелизма и памяти для хранилища данных SQL Azure](memory-and-concurrency-limits.md).
* **DatabaseName**: имя создаваемого Хранилища данных SQL.
* **ServerName**: имя сервера, который используется для создания.
* **ResourceGroupName**: используемая группа ресурсов. Чтобы найти доступные группы ресурсов, входящие в вашу подписку, используйте командлет Get-AzureResource.
* **Выпуск**: для создания Хранилища данных SQL необходим выпуск DataWarehouse.

Необязательные параметры.

- **CollationName**: если параметры сортировки не указаны, по умолчанию используется SQL_Latin1_General_CP1_CI_AS. Параметры сортировки базы данных изменить нельзя.
- **MaxSizeBytes**: по умолчанию максимальный размер базы данных составляет 240 ТБ. Максимальный размер ограничивает объем данных хранимых строк. Объем данных хранения для столбцов неограничен.

Дополнительные сведения о параметрах см. в статье о командлете [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).


## <a name="clean-up-resources"></a>Очистка ресурсов

Другие краткие руководства в этой серии созданы на основе этого документа. 

> [!TIP]
> Если вы собираетесь продолжать работу с ними, не удаляйте ресурсы, которые вы создали при работе с этим руководством. В противном случае удалите все созданные ресурсы, выполнив на портале Azure следующие действия.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Дополнительная информация

Вы создали хранилище данных, создали правило брандмауэра, подключились к этому хранилищу данных и выполнили несколько запросов. Чтобы узнать больше о хранилище данных SQL Azure, перейдите к руководству по загрузке данных.
> [!div class="nextstepaction"]
>[Загрузка данных в хранилище данных SQL](load-data-from-azure-blob-storage-using-polybase.md)
