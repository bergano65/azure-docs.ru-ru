---
title: Шаблоны Azure Resource Manager для API таблицы Azure Cosmos DB
description: Используйте шаблоны Azure Resource Manager для создания и настройки API таблицы Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 82e2a436bf6b25b6164d845d234896390a262292
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968823"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Управление ресурсами API таблицы Azure Cosmos DB, с помощью шаблонов Azure Resource Manager

## Создание учетной записи Azure Cosmos и таблицы <a id="create-resource"></a>

Создание ресурсов Azure Cosmos DB, с помощью шаблона Azure Resource Manager. Этот шаблон будет создать учетную запись Azure Cosmos для API таблицы с одной таблицей 400 ЕЗ/с пропускной способностью. Скопируйте шаблон и развернуть, как показано ниже, [коллекции быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) и развертывание с помощью портала Azure. Можно также загрузить шаблон локального компьютера или создать новый шаблон и указать локальный путь с `--template-file` параметра.

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Развертывание с помощью PowerShell

Чтобы развернуть шаблон Resource Manager с помощью PowerShell, **копирования** скрипт и выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Если вы решили использовать локально установленной версии PowerShell, а не из Azure Cloud shell, вам нужно [установить](/powershell/azure/install-az-ps) модуля Azure PowerShell. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`.

### <a name="deploy-via-azure-cli"></a>Развертывание с помощью Azure CLI

Чтобы развернуть шаблон Resource Manager с помощью Azure CLI, **копирования** скрипт и выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` Команда показывает созданной учетной записи Azure Cosmos, после его подготовки. Если вы решили использовать локально установленная версия Azure CLI, а не CloudShell, см. в разделе [интерфейса командной строки Azure (CLI)](/cli/azure/) статьи.

## Обновить пропускную способность (ЕЗ/с) для таблицы <a id="table-ru-update"></a>

Следующий шаблон обновит пропускную способность таблицы. Скопируйте шаблон и развернуть, как показано ниже, [коллекции быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table-ru-update/) и развертывание с помощью портала Azure. Можно также загрузить шаблон локального компьютера или создать новый шаблон и указать локальный путь с `--template-file` параметра.

[!code-json[cosmosdb-table-ru-update](~/quickstart-templates/101-cosmosdb-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-throughput-via-powershell"></a>Развертывание пропускную способность таблицы с помощью PowerShell

Чтобы развернуть шаблон Resource Manager с помощью PowerShell, **копирования** скрипт и выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$tableName = Read-Host -Prompt "Enter the table name"
$throughput = Read-Host -Prompt "Enter new throughput for table"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -tableName $tableName `
    -throughput $throughput
```

### <a name="deploy-table-template-via-azure-cli"></a>Развертывание шаблона таблицы с помощью Azure CLI

Чтобы развернуть шаблон Resource Manager с помощью интерфейса командной строки Azure, выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the table name: ' tableName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json \
   --parameters accountName=$accountName tableName=$tableName throughput=$throughput
```

## <a name="next-steps"></a>Следующие шаги

Ниже приведены некоторые дополнительные ресурсы.

- [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
- [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Шаблоны Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Устранение распространенных ошибок развертывания Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)