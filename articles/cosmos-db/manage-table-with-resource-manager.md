---
title: Шаблоны менеджера ресурсов для API таблицы таблицы Azure Cosmos
description: Используйте шаблоны менеджера ресурсов Azure для создания и настройки API таблицы таблицы Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 6ab54e56368e7e26e807e4d1dc0592536dc9374a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246712"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Управление aPI-ресурсами таблицы AB Azure Cosmos, используя шаблоны управления ресурсами Azure

В этой статье описывается, как выполнять различные операции для автоматизации управления учетными записями, базами данных и контейнерами Azure Cosmos, используя шаблоны Azure Resource Manager. В этой статье есть примеры только для учетных записей Table API, чтобы найти примеры для других учетных записей типа API: используйте шаблоны менеджера ресурсов Azure Саус с API Azure Cosmos DB для [Cassandra,](manage-cassandra-with-resource-manager.md) [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [S'L](manage-sql-with-resource-manager.md) статьи.

## <a name="create-azure-cosmos-account-and-table"></a>Создание учетной записи и таблицы Azure Cosmos<a id="create-resource"></a>

Создавайте dB-ресурсы Azure Cosmos с помощью шаблона управления ресурсами Azure. Этот шаблон создаст учетную запись Azure Cosmos для API таблицы с одной таблицей на 400 RU/s пропускной способности. Копируйте шаблон и развертывайте, как показано ниже, или посетите [галерею быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) и разместите его с портала Azure. Вы также можете загрузить шаблон на локальном компьютере или создать `--template-file` новый шаблон и указать локальный путь с параметром.

> [!NOTE]
> Имена учетных записей должны быть в нижнем регистре и 44 или меньше символов.
> Чтобы обновить RU/s, повторно отправьте шаблон с обновленными значениями свойств пропускной способности.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

### <a name="deploy-via-powershell"></a>Развертывание через PowerShell

Чтобы развернуть шаблон менеджера ресурсов с помощью PowerShell, **скопируйте** скрипт и выберите **«Попробуйте открыть** облачную оболочку Azure. Чтобы вставить сценарий, нажмите правой кнопкой оболочки, а затем выберите **Паста:**

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

Если вы решите использовать локально установленную версию PowerShell вместо оболочки Облака Azure, необходимо [установить](/powershell/azure/install-az-ps) модуль Azure PowerShell. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`.

### <a name="deploy-via-the-azure-cli"></a>Развертывание через Azure CLI

Чтобы развернуть шаблон менеджера ресурсов Azure с помощью CLI Azure, **скопируйте** скрипт и **поберите «Попробуйте открыть** — облачную оболочку Azure. Чтобы вставить сценарий, нажмите правой кнопкой оболочки, а затем выберите **Паста:**

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

Команда `az cosmosdb show` показывает недавно созданную учетную запись Azure Cosmos после того, как она была подготовлена. Если вместо оболочки —сяра— местная версия CLI, установленная локально, смотрите статью [Azure CLI.](/cli/azure/)

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены некоторые дополнительные ресурсы.

- [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
- [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Шаблоны Azure Cosmos DB Квикстарт](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Устранение проблем общие ошибки в развертывании ресурсов Ресурсов Azure](../azure-resource-manager/templates/common-deployment-errors.md)