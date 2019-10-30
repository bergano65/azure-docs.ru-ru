---
title: Создание Azure Cosmos DB и управление ими с помощью шаблонов Azure Resource Manager
description: Использование шаблонов Azure Resource Manager для создания и настройки Azure Cosmos DB для API-интерфейса SQL (Core)
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mjbrown
ms.openlocfilehash: 378deb2138acf2a2c33860ab4e4ebcc44a57d8cd
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053324"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-using-azure-resource-manager-templates"></a>Управление ресурсами API Azure Cosmos DB SQL (Core) с помощью шаблонов Azure Resource Manager

## Создание учетной записи, базы данных и контейнера Azure Cosmos<a id="create-resource"></a>

Создание Azure Cosmos DB ресурсов с помощью шаблона Azure Resource Manager. Этот шаблон создаст учетную запись Azure Cosmos с двумя контейнерами, которые совместно используют пропускную способность 400 единиц запросов/с на уровне базы данных. Скопируйте шаблон и разверните его, как показано ниже, или откройте коллекцию быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) и выполните развертывание из портал Azure. Можно также загрузить шаблон на локальный компьютер или создать новый шаблон и указать локальный путь с помощью параметра `--template-file`.

> [!NOTE]
>
> - Вы не можете одновременно добавлять или удалять расположения в учетной записи Azure Cosmos и изменять другие свойства. Они должны выполняться как отдельные операции.
> - Имена учетных записей должны содержать строчные буквы и < 31 символ.

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Развертывание с помощью PowerShell

Чтобы развернуть шаблон диспетчер ресурсов с помощью PowerShell, **скопируйте** скрипт и выберите **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

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

Если вы решили использовать локально установленную версию PowerShell, а не Azure Cloud Shell, необходимо [установить](/powershell/azure/install-az-ps) модуль Azure PowerShell. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`.

### <a name="deploy-via-azure-cli"></a>Развертывание с помощью Azure CLI

Чтобы развернуть шаблон диспетчер ресурсов с помощью Azure CLI, выберите команду **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

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

Команда `az cosmosdb show` отображает созданную учетную запись Azure Cosmos после ее подготовки. Если вы решили использовать локально установленную версию Azure CLI вместо использования CloudShell, см. статью [интерфейс командной строки Azure (CLI)](/cli/azure/) .

## Создание контейнера Azure Cosmos DB с функциональностью на стороне сервера<a id="create-sproc"></a>

Создайте контейнер Azure Cosmos DB с хранимой процедурой, триггером и определяемой пользователем функцией с помощью шаблона Azure Resource Manager. Скопируйте шаблон и разверните его, как показано ниже, или откройте коллекцию быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) и выполните развертывание из портал Azure. Можно также загрузить шаблон на локальный компьютер или создать новый шаблон и указать локальный путь с помощью параметра `--template-file`.

[!code-json[create-cosmosdb-sql-sprocs](~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json)]

### <a name="deploy-stored-procedure-template-via-powershell"></a>Развертывание шаблона хранимой процедуры с помощью PowerShell

Чтобы развернуть шаблон диспетчер ресурсов с помощью PowerShell, **скопируйте** скрипт и выберите **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Если вы решили использовать локально установленную версию PowerShell, а не Azure Cloud Shell, необходимо [установить](/powershell/azure/install-az-ps) модуль Azure PowerShell. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`.

### <a name="deploy-stored-procedure-template-via-azure-cli"></a>Развертывание шаблона хранимой процедуры с помощью Azure CLI

Чтобы развернуть шаблон диспетчер ресурсов с помощью Azure CLI, выберите команду **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## Обновление пропускной способности (единиц запросов/с) в базе данных<a id="database-ru-update"></a>

В следующем шаблоне будет обновлена пропускная способность базы данных. Скопируйте шаблон и разверните его, как показано ниже, или откройте коллекцию быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-database-ru-update/) и выполните развертывание из портал Azure. Можно также загрузить шаблон на локальный компьютер или создать новый шаблон и указать локальный путь с помощью параметра `--template-file`.

[!code-json[cosmosdb-sql-database-ru-update](~/quickstart-templates/101-cosmosdb-sql-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-powershell"></a>Развертывание шаблона базы данных с помощью PowerShell

Чтобы развернуть шаблон диспетчер ресурсов с помощью PowerShell, **скопируйте** скрипт и выберите **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

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

### <a name="deploy-database-template-via-azure-cli"></a>Развертывание шаблона базы данных с помощью Azure CLI

Чтобы развернуть шаблон диспетчер ресурсов с помощью Azure CLI, выберите команду **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Обновление пропускной способности (единиц запросов/с) в контейнере<a id="container-ru-update"></a>

В следующем шаблоне будет обновлена пропускная способность контейнера. Скопируйте шаблон и разверните его, как показано ниже, или откройте коллекцию быстрого запуска [Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-ru-update/) и выполните развертывание из портал Azure. Можно также загрузить шаблон на локальный компьютер или создать новый шаблон и указать локальный путь с помощью параметра `--template-file`.

[!code-json[cosmosdb-sql-container-ru-update](~/quickstart-templates/101-cosmosdb-sql-container-ru-update/azuredeploy.json)]

### <a name="deploy-container-template-via-powershell"></a>Развертывание шаблона контейнера с помощью PowerShell

Чтобы развернуть шаблон диспетчер ресурсов с помощью PowerShell, **скопируйте** скрипт и выберите **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

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

### <a name="deploy-container-template-via-azure-cli"></a>Развертывание шаблона контейнера с помощью Azure CLI

Чтобы развернуть шаблон диспетчер ресурсов с помощью Azure CLI, выберите команду **попробовать** , чтобы открыть Azure Cloud Shell. Чтобы вставить скрипт, щелкните оболочку правой кнопкой мыши и выберите команду **Вставить**:

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
- [Шаблоны быстрого запуска Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Устранение распространенных ошибок развертывания Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)
