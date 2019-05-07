---
title: Шаблоны Azure Resource Manager для API Azure Cosmos DB для MongoDB
description: Используйте шаблоны Azure Resource Manager для создания и настройки Azure Cosmos DB API для MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: bcf9e0492e58de79efbb16f9ee13adbd0f27b572
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077775"
---
# <a name="create-azure-cosmos-db-api-for-mongodb-resources-from-a-resource-manager-template"></a>Создание Azure Cosmos DB API для MongoDB ресурсов с помощью шаблона Resource Manager

Сведения о создании API в Azure Cosmos DB для MongoDB ресурсов с помощью шаблона Azure Resource Manager. В следующем примере создается учетная запись Azure Cosmos DB для API MongoDB из [шаблон быстрого запуска Azure](https://aka.ms/mongodb-arm-qs). Этот шаблон создаст учетную запись Azure Cosmos для MongoDB API с помощью двух коллекций, которые совместно используют 400 ЕЗ/с пропускной способности на уровне базы данных.

Ниже приведена копия шаблона:

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

## <a name="deploy-via-azure-cli"></a>Развертывание с помощью Azure CLI

Чтобы развернуть шаблон Resource Manager с помощью Azure CLI, **копирования** скрипт и выберите **попробовать** чтобы открыть Azure Cloud shell. Чтобы скопировать скрипт, щелкните правой кнопкой мыши среду и выберите **вставьте**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` Команда показывает созданной учетной записи Azure Cosmos, после его подготовки. Если вы решили использовать локально установленная версия Azure CLI, а не CloudShell, см. в разделе [интерфейса командной строки Azure (CLI)](/cli/azure/) статьи.

В предыдущем примере ссылка на шаблон, который хранится в GitHub. Можно также загрузить шаблон локального компьютера или создать новый шаблон и указать локальный путь с `--template-file` параметра.

## <a name="next-steps"></a>Следующие шаги

Ниже приведены некоторые дополнительные ресурсы.

- [Документация по Azure Resource Manager](/azure/azure-resource-manager/)
- [Схема поставщика ресурсов Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Шаблоны Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Устранение распространенных ошибок развертывания Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)