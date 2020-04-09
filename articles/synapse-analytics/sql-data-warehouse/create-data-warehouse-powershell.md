---
title: Создание пула SQL Synapse и отправка в него запросов с помощью Azure PowerShell
description: Сведения о том, как быстро создать логический сервер пула SQL Synapse с правилом брандмауэра на уровне сервера с помощью Azure PowerShell.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 57564e9dffd6022e1e4fe464b4b26a5bb8eb318b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631329"
---
# <a name="quickstart-create-and-query-a-synapse-sql-pool-with-azure-powershell"></a>Краткое руководство. Создание пула SQL Synapse и отправка в него запросов с помощью Azure PowerShell

Создайте пул SQL Synapse (хранилище данных) в Azure Synapse Analytics с помощью Azure PowerShell.

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

> [!IMPORTANT]
> Создание пула SQL может повлечь дополнительные расходы.  Дополнительные сведения см. на странице [цен на Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Вход в Azure

С помощью команды [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) войдите в подписку Azure и следуйте инструкциям на экране.

```powershell
Connect-AzAccount
```

Чтобы узнать, какие подписки вы используете, выполните [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Если необходимо использовать подписку не по умолчанию, выполните [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

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
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов Azure ](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) с помощью команды [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа. В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении именем `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-logical-server"></a>Создание логического сервера

Создайте [логический сервер SQL Azure](../../sql-database/sql-database-logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) с помощью команды [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Логический сервер содержит группу баз данных, которыми можно управлять как группой. В примере ниже показано создание сервера со случайным именем в группе ресурсов с именем администратора `ServerAdmin` и паролем `ChangeYourAdminPassword1`. Замените эти предопределенные значения по своему усмотрению.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Настройка правил брандмауэра сервера

Создайте [правило брандмауэра на уровне сервера SQL Azure](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) с помощью команды [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Правило брандмауэра на уровне сервера позволяет внешним приложениям, таким как SQL Server Management Studio или программе SQLCMD, подключаться к пулу SQL через брандмауэр службы пула SQL.

В следующем примере брандмауэр открыт только для других ресурсов Azure. Чтобы включить возможность внешнего подключения, измените IP-адрес на соответствующий адрес своей среды. Чтобы открыть все IP-адреса, используйте 0.0.0.0 как начальный IP-адрес, а 255.255.255.255 — как конечный.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> Конечные точки SQL взаимодействуют через порт 1433. Если вы пытаетесь подключиться из корпоративной сети, исходящий трафик через порт 1433 может быть запрещен сетевым брандмауэром. В таком случае вы не сможете подключиться к серверу SQL Azure. Для этого ваш ИТ-отдел должен открыть порт 1433.
>

## <a name="create-a-sql-pool"></a>Создание пула SQL

В следующем примере создается пул SQL с использованием ранее определенных переменных.  Он определяет цель службы как DW100c, что является бюджетной начальной точкой для пула SQL.

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

* **RequestedServiceObjectiveName**: количество запрашиваемых [единиц хранилища данных](what-is-a-data-warehouse-unit-dwu-cdwu.md). Увеличение этого количества приведет к повышению стоимости вычислений. Список поддерживаемых значений см. в статье [Ограничения параллелизма и памяти для хранилища данных SQL Azure](memory-concurrency-limits.md).
* **DatabaseName**: Имя создаваемого пула SQL.
* **ServerName**: имя сервера, который используется для создания.
* **ResourceGroupName**: используемая группа ресурсов. Чтобы найти доступные группы ресурсов, входящие в вашу подписку, используйте командлет Get-AzureResource.
* **Выпуск**: для создания пула SQL необходим выпуск DataWarehouse.

Необязательные параметры.

* **CollationName**: если параметры сортировки не указаны, по умолчанию используется SQL_Latin1_General_CP1_CI_AS. Параметры сортировки базы данных изменить нельзя.
* **MaxSizeBytes**: по умолчанию максимальный размер базы данных составляет 240 ТБ. Максимальный размер ограничивает объем данных хранимых строк. Объем данных хранения для столбцов неограничен.

Дополнительные сведения о параметрах см. в статье о командлете [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="clean-up-resources"></a>Очистка ресурсов

Другие краткие руководства в этой серии созданы на основе этого документа.

> [!TIP]
> Если вы собираетесь продолжать работу с ними, не удаляйте ресурсы, которые вы создали при работе с этим руководством. В противном случае удалите все созданные ресурсы, выполнив на портале Azure следующие действия.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Дальнейшие действия

Вы создали пул SQL, создали правило брандмауэра, подключились к этому пулу SQL и выполнили несколько запросов. Чтобы узнать больше, перейдите к статье о [загрузке данных в пул SQL](load-data-from-azure-blob-storage-using-polybase.md).
