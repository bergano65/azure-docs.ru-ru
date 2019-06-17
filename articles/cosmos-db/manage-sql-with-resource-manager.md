---
title: Создание и управление ими с помощью шаблонов Azure Resource Manager в Azure Cosmos DB
description: Использование шаблонов Azure Resource Manager для создания и настройки Azure Cosmos DB для SQL (Core) API
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: mjbrown
ms.openlocfilehash: 5683fd072961c7793d8f4bbeb9ecc16a93dd7373
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242591"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-using-azure-resource-manager-templates"></a>Управлять ресурсами SQL Azure Cosmos DB (Core) API, с помощью шаблонов Azure Resource Manager

## Создание учетной записи, базы данных и контейнер Azure Cosmos <a id="create-resource"></a>

Создание ресурсов Azure Cosmos DB, с помощью шаблона Azure Resource Manager. Этот шаблон создаст учетную запись Azure Cosmos с двумя контейнерами, которые совместно используют 400 ЕЗ/с пропускной способности на уровне базы данных. Скопируйте шаблон и развернуть, как показано ниже, [коллекции быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) и развертывание с помощью портала Azure. Можно также загрузить шаблон локального компьютера или создать новый шаблон и указать локальный путь с `--template-file` параметра.

> [!NOTE]
> В настоящее время нельзя развернуть Functions(UDFs) определенные пользователем, хранимые процедуры и триггеры с помощью шаблонов Resource Manager. 

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Развертывание с помощью PowerShell

Чтобы развернуть шаблон Resource Manager с помощью PowerShell, **копирования** скрипт и выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$container1Name = Read-Host -Prompt "Enter the first container name"
$container2Name = Read-Host -Prompt "Enter the second container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -container1Name $container1Name `
    -container2Name $container2Name

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Если вы решили использовать локально установленной версии PowerShell, а не из Azure Cloud shell, вам нужно [установить](/powershell/azure/install-az-ps) модуля Azure PowerShell. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`.

### <a name="deploy-via-azure-cli"></a>Развертывание с помощью Azure CLI

Чтобы развернуть шаблон Resource Manager с помощью интерфейса командной строки Azure, выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first container name: ' container1Name
read -p 'Enter the second container name: ' container2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   container1Name=$container1Name container2Name=$container2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` Команда показывает созданной учетной записи Azure Cosmos, после его подготовки. Если вы решили использовать локально установленная версия Azure CLI, а не CloudShell, см. в разделе [интерфейса командной строки Azure (CLI)](/cli/azure/) статьи.

## Обновить пропускную способность (ЕЗ/с) в базе данных <a id="database-ru-update"></a>

Следующий шаблон обновит пропускной способности базы данных. Скопируйте шаблон и развернуть, как показано ниже, [коллекции быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-database-ru-update/) и развертывание с помощью портала Azure. Можно также загрузить шаблон локального компьютера или создать новый шаблон и указать локальный путь с `--template-file` параметра.

[!code-json[cosmosdb-sql-database-ru-update](~/quickstart-templates/101-cosmosdb-sql-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-powershell"></a>Развертывание шаблона базы данных с помощью PowerShell

Чтобы развернуть шаблон Resource Manager с помощью PowerShell, **копирования** скрипт и выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$databaseName = Read-Host -Prompt "Enter the database name"
$throughput = Read-Host -Prompt "Enter new throughput for database"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -databaseName $databaseName `
    -throughput $throughput
```

### <a name="deploy-database-template-via-azure-cli"></a>Развертывание шаблона базы данных с помощью интерфейса командной строки Azure

Чтобы развернуть шаблон Resource Manager с помощью интерфейса командной строки Azure, выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Обновить контейнере пропускную способность (ЕЗ/с) <a id="container-ru-update"></a>

Следующий шаблон обновит пропускной способности контейнера. Скопируйте шаблон и развернуть, как показано ниже, [коллекции быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-ru-update/) и развертывание с помощью портала Azure. Можно также загрузить шаблон локального компьютера или создать новый шаблон и указать локальный путь с `--template-file` параметра.

[!code-json[cosmosdb-sql-container-ru-update](~/quickstart-templates/101-cosmosdb-sql-container-ru-update/azuredeploy.json)]

### <a name="deploy-container-template-via-powershell"></a>Развертывание шаблона контейнера с помощью PowerShell

Чтобы развернуть шаблон Resource Manager с помощью PowerShell, **копирования** скрипт и выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"
$throughput = Read-Host -Prompt "Enter new throughput for container"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -databaseName $databaseName `
    -containerName $containerName `
    -throughput $throughput
```

### <a name="deploy-container-template-via-azure-cli"></a>Развертывание шаблона контейнера через интерфейс командной строки Azure

Чтобы развернуть шаблон Resource Manager с помощью интерфейса командной строки Azure, выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName containerName=$containerName throughput=$throughput
```

## <a name="next-steps"></a>Следующие шаги

Ниже приведены некоторые дополнительные ресурсы.

- [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
- [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Шаблоны Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Устранение распространенных ошибок развертывания Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)