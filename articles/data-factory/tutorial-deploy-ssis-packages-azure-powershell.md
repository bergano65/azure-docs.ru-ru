---
title: Подготовка среды выполнения интеграции Azure SSIS с помощью PowerShell | Документация Майкрософт
description: Узнайте, как с помощью PowerShell подготовить среду выполнения интеграции Azure SSIS в фабрике данных Azure, чтобы запускать и развертывать пакеты служб SSIS в Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bbbbc45bd050b8f68499febeed6285ad1aa883c4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484777"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Подготовка среды выполнения интеграции Azure SSIS в фабрике данных Azure с помощью PowerShell

В этом руководстве представлены шаги по подготовке среды выполнения интеграции Azure SQL Server Integration Services (SSIS) в Фабрике данных Azure (ADF). Azure-SSIS IR поддерживает запуск пакетов, развернутых в каталоге SSIS (SSISDB), который размещен на сервере Базы данных SQL Azure или Управляемом экземпляре (модель развертывания проекта), а также развернутых в файловых системах, общих папках или Файлах Azure (модель развертывания пакетов). После подготовки Azure-SSIS IR вы можете использовать привычные средства, такие как SQL Server Data Tools (SSDT) или SQL Server Management Studio (SSMS), и программы командной строки, такие как `dtinstall`/`dtutil`/`dtexec`, для развертывания и запуска пакетов в Azure. Вот какие шаги выполняются в этом руководстве:

> [!NOTE]
> В этой статье используется Azure PowerShell для подготовки Azure-SSIS IR. Чтобы использовать портал Azure / приложение ADF для подготовки Azure-SSIS IR см. статью [ Подготовка Integration Runtime Azure – SSIS в Фабрике данных Azure](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Создали фабрику данных.
> * Создание среды выполнения интеграции Azure SSIS.
> * Запуск среды выполнения интеграции Azure SSIS.
> * Проверка всего скрипта.
> * Развертывание пакетов служб SSIS.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Подписка Azure**. Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу. См. дополнительные сведения о [среде выполнения интеграции Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).
- **Сервер Базы данных SQL Azure (необязательно)** . Если у вас еще нет сервера базы данных, создайте его на портале Azure перед началом работы. ADF, в свою очередь, создаст SSISDB на этом сервере базы данных. Мы рекомендуем создать сервер базы данных в одном регионе Azure со средой интеграции. Эта конфигурация позволяет среде выполнения интеграции записывать журналы выполнения в SSISDB, не пересекая регионы Azure. 
    - В зависимости от выбранного сервера базы данных SSISDB может быть создана от вашего имени как отдельная база данных, а также как часть эластичного пула или Управляемого экземпляра. Доступ к ней можно получить через общедоступную сеть или после присоединения к виртуальной сети. Инструкции по выбору типа сервера базы данных для размещения SSISDB см. в разделе [Compare Azure SQL Database single database/elastic pool/Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance) (Сравнение отдельной базы данных, эластичного пула и Управляемого экземпляра Базы данных SQL Azure). Если сервер Базы данных SQL Azure используется с конечными точками служб для виртуальной сети или Управляемым экземпляром в виртуальной сети с целью размещения SSISDB или требования доступа к локальным данным, необходимо присоединить к виртуальной сети вашу среду выполнения интеграции Azure SSIS. Сведения об этом см. в статье [Создание среды выполнения интеграции Azure SSIS в службе "Фабрика данных Azure"](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Убедитесь, что для сервера базы данных включен параметр **Разрешить доступ к службам Azure**. Он не применяется, когда сервер Базы данных SQL Azure используется с конечными точками служб для виртуальной сети или Управляемым экземпляром в виртуальной сети с целью размещения базы данных SSISDB. Дополнительные сведения см. в разделе [Создание правила брандмауэра на уровне сервера с помощью портала Azure](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Сведения о включении этого параметра с помощью PowerShell см. в статье [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Добавьте IP-адрес клиентского компьютера или диапазон IP-адресов, который включает IP-адрес клиентского компьютера, в список IP-адресов клиента в параметрах брандмауэра для сервера базы данных. Дополнительные сведения см. в разделе [Правила брандмауэра уровня сервера Базы данных SQL Azure и уровня базы данных SQL Azure](../sql-database/sql-database-firewall-configure.md).
    - К серверу базы данных можно подключиться с использованием аутентификации SQL и учетных данных администратора сервера или аутентификации Azure Active Directory (AAD) и управляемого удостоверения ADF.  Для последнего варианта управляемое удостоверение ADF нужно добавить в группу AAD, которая обладает разрешениями на доступ к серверу базы данных. См.статью [Создание среды выполнения интеграции Azure SSIS в службе "Фабрика данных Azure"](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Убедитесь, что на сервере базы данных уже нет SSISDB. При подготовке среды выполнения интеграции Azure-SSIS не поддерживается использование существующей SSISDB.
- **Azure PowerShell**. Если вы хотите выполнить подготовку среды выполнения интеграции Azure-SSIS с помощью скрипта PowerShell, следуйте инструкциям в разделе [Установка модуля Azure PowerShell](/powershell/azure/install-Az-ps).

> [!NOTE]
> - Список регионов Azure, в которых сейчас доступны ADF и среда выполнения интеграции Azure-SSIS, см. на странице [Доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="launch-windows-powershell-ise"></a>Запуск интегрированной среды сценариев Windows PowerShell

Откройте **интегрированную среду сценариев Windows PowerShell** с правами администратора. 

## <a name="create-variables"></a>Создание переменных

Скопируйте и вставьте следующий скрипт. Укажите значения для переменных. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

## <a name="sign-in-and-select-subscription"></a>Вход и выбор подписки

Чтобы войти и выбрать подписку Azure, добавьте следующий код скрипта.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-database-server"></a>Проверьте подключение к серверу базы данных

Добавьте приведенный ниже скрипт для проверки сервера Базы данных SQL Azure. 

```powershell
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}
```

Чтобы создать Базу данных Azure SQL в рамках скрипта, см. следующий пример: 

Задайте значения для переменных, которые еще не были определены. Например:  SSISDBServerName, FirewallIPAddress. 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов Azure ](../azure-resource-manager/resource-group-overview.md) с помощью команды [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа.

Если группа ресурсов уже существует, не копируйте этот код в скрипт. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Создание фабрики данных

Чтобы создать фабрику данных, выполните следующие команды:

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Создание среды выполнения интеграции

Выполните следующую команду для создания среды выполнения интеграции Azure SSIS, запускающей пакеты служб SSIS в Azure:

Если вы не используете SSISDB, вы можете опустить параметры CatalogServerEndpoint, CatalogPricingTier и CatalogAdminCredential.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

## <a name="start-integration-runtime"></a>Запуск среды выполнения интеграции

Выполните следующие команды для запуска среды выполнения интеграции SSIS Azure.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

> [!NOTE]
> За исключением времени для выполнения пользовательской настройки, этот процесс должен быть завершен в течение пяти минут.
>
> Если вы используете SSISDB, служба ADF подключится к серверу базы данных для подготовки SSISDB. 
> 
> При подготовке Azure-SSIS IR также устанавливается распространяемый компонент Access и пакет функций Azure для служб SSIS. Эти компоненты обеспечивают подключение к файлам Excel и Access и другим источникам данных Azure в дополнение к тем, которые уже поддерживаются встроенными компонентами. Вы также можете установить дополнительные компоненты (см. статью [Пользовательская установка для среды выполнения интеграции Azure–SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)).

## <a name="full-script"></a>Полный сценарий

Скрипт PowerShell в этом разделе настраивает экземпляр Azure-SSIS IR, который запускает пакеты SSIS. После успешного запуска скрипта можно развернуть и выполнить пакет SSIS в Azure.

1. Откройте интегрированную среду сценариев (ISE) Windows PowerShell.
2. В ISE введите в окне командной строки следующую команду:  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. Скопируйте скрипт PowerShell в этом разделе и вставьте его в ISE.
4. Укажите соответствующие значения для всех параметров в начале скрипта.
5. Выполните скрипт. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you want to use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")   
```

## <a name="monitor-and-manage-azure-ssis-ir"></a>Мониторинг среды выполнения интеграции IR Azure SSIS и управление ею

Сведения о мониторинге и администрировании среды выполнения интеграции см. по ссылкам ниже. 

- [Среда выполнения интеграции Azure SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).
- [Перенастройка среды выполнения интеграции SQL Server Integration Services Azure](manage-azure-ssis-integration-runtime.md).

## <a name="deploy-ssis-packages"></a>Развертывание пакетов служб SSIS.

Если вы используете SSISDB, вы можете развернуть в ней пакеты и запустить их в Azure-SSIS IR с помощью инструментов SSDT или SSMS, которые подключаются к серверу базы данных через конечную точку сервера. Для сервера Базы данных SQL Azure или Управляемого экземпляра с общедоступной конечной точкой формат конечной точки сервера будет `<server name>.database.windows.net`/`<server name>.public.<dns prefix>.database.windows.net,3342` соответственно. Если вы не используете SSISDB, вы можете развернуть пакеты в файловых системах, общих папках или в службе "Файлы Azure" и запустить их в Azure-SSIS IR с помощью программ командной строки `dtinstall`/`dtutil`/`dtexec`. Дополнительные сведения см. в разделе [Развертывание пакетов на сервере служб Integration Services](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). В обоих случаях вы также можете запустить развернутые пакеты в Azure-SSIS IR, используя действие "Выполнение пакетов служб SSIS" в конвейерах ADF (см. статью [Запуск пакета Integration Services с помощью действия выполнения пакета SSIS в Фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)).

См. также документацию SSIS в следующих статьях: 

- [Руководство. Развертывание и выполнение пакета служб SQL Server Integration Services (SSI) в Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Подключение к каталогу SSIS (SSISDB) в Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Планирование выполнения пакетов служб SQL Server Integration Services (SSIS), развернутых в Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows Authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Подключение к локальным источникам данных с помощью аутентификации Windows) 

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи: 

> [!div class="checklist"]
> * Создали фабрику данных.
> * Создание среды выполнения интеграции Azure SSIS.
> * Запуск среды выполнения интеграции Azure SSIS.
> * Проверка всего скрипта.
> * Развертывание пакетов служб SSIS.

Дополнительные сведения о настройке Azure-SSIS Integration Runtime см. в следующей статье:

> [!div class="nextstepaction"]
>[Настройка Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)