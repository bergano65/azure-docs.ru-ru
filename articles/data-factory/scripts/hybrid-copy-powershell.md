---
title: 'Скрипт PowerShell: копирование данных из локальной среды в Azure с помощью Фабрики данных | Документация Майкрософт'
description: Этот сценарий PowerShell копирует данные из локальной базы данных SQL Server в другое хранилище BLOB-объектов Azure.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: 1218408bee067d6edb274fdcbf4fae62b8245a01
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160668"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>Создание конвейера фабрики данных для копирования данных из локальной среды в Azure с помощью PowerShell

Этот пример сценария PowerShell создает конвейер в фабрике данных Azure, который копирует данные локальной базы данных SQL Server в другое в хранилище BLOB-объектов Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Технические условия

- **SQL Server.** В этом примере в качестве **исходного** хранилища данных используется локальная база данных SQL Server.
- **Учетная запись хранения Azure.** В этом примере в качестве **места назначения и приемника** будет использоваться хранилище BLOB-объектов Azure. в статье [Об учетных записях хранения Azure](../../storage/common/storage-quickstart-create-account.md) .
- **Локальная среда выполнения интеграции**. Скачайте MSI-файл из [центра загрузки](https://www.microsoft.com/download/details.aspx?id=39717) и запустите его, чтобы установить локальную среду выполнения интеграции на компьютере.  

### <a name="create-sample-database-in-sql-server"></a>Создание примера базы данных в SQL Server
1. В локальной базе данных SQL Server создайте таблицу **emp**, используя следующий сценарий SQL. 

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Вставьте в эту таблицу примеры данных.

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Пример скрипта

> [!IMPORTANT]
> Этот скрипт создает JSON-файлы, определяющие сущности фабрики данных (связанную службу, набор данных и конвейер) на жестком диске в папке c:\.

```powershell
$resourceGroupName = "<Resource group name>"
$dataFactoryName = "<Data factory name>" # must be globally unique
$storageAccountName = "<Az.Storage account name>"
$storageAccountKey = "<Az.Storage account key>"
$sqlServerName = "<SQL server name>"
$sqlDatabaseName = "SQL Server database name"
$sqlTableName = "emp" # create the emp table if it does not already exist in your database with ID, FirstName, and LastName columns of type String. 
$sqlUserName = "<SQL Authentication - user name>"
$sqlPassword = "<SQL Authentication - user password>"
$blobFolderPath = "<Azure blob container name>/<Azure blob folder name>"
$integrationRuntimeName = "<Self-hosted integration runtime name"
$pipelineName = "SqlServerToBlobPipeline"
$dataFactoryRegion = "China East"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $dataFactoryRegion

# create a data factory
$df = Set-AzDataFactory -ResourceGroupName $resourceGroupName -Name $dataFactoryName -Location $dataFactoryRegion

# create a self-hosted integration runtime
Set-AzDataFactoryIntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName

# get the authorization key from the created integration runtime in the cloud
Get-AzDataFactoryIntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json

# IMPORTANT: Install self-hosted integration runtime on your machine and use one of the keys to register the IR installed on your machine with the cloud service

# create an Az.Storage linked service

## JSON definition of the linked service. 
$storageLinkedServiceDefinition = @"
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey",
                "type": "SecureString"
            }
        }
    }
}
"@

## IMPORTANT: stores the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$storageLinkedServiceDefinition | Out-File c:\AzureStorageLinkedService.json

## Creates a linked service in the data factory
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File c:\AzureStorageLinkedService.json

# create an on-premises SQL Server linked service

## JSON definition of the linked service. 
$sqlServerLinkedServiceDefinition = @"
{
   "properties": {
     "type": "SqlServer",
     "typeProperties": {
         "connectionString": {
             "type": "SecureString",
            "value": "Server=$sqlServerName;Database=$sqlDatabaseName;User ID=$sqlUserName;Password=$sqlPassword;Timeout=60"
         }
     },
     "connectVia": {
       "type": "integrationRuntimeReference",
       "referenceName": "$integrationRuntimeName"
     }
 },
 "name": "SqlServerLinkedService"
}
"@

## IMPORTANT: stores the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$sqlServerLinkedServiceDefinition | Out-File c:\SqlServerLinkedService.json

## Encrypt SQL Server credentials 
New-AzDataFactoryLinkedServiceEncryptCredential -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File "c:\SqlServerLinkedService.json" > c:\EncryptedSqlServerLinkedService.json

# Create a SQL Server linked service
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName "EncryptedSqlServerLinkedService" -File "c:\EncryptedSqlServerLinkedService.json"


# Create a source dataset for source SQL Server Database

## JSON definition of the dataset
$sourceSqlServerDatasetDefiniton = @"
{
   "properties": {
        "type": "SqlServerTable",
        "typeProperties": {
            "tableName": "$sqlTableName"
        },
        "structure": [
             {
                "name": "ID",
                "type": "String"
            },
            {
                "name": "FirstName",
                "type": "String"
            },
            {
                "name": "LastName",
                "type": "String"
            }
        ],
        "linkedServiceName": {
            "referenceName": "EncryptedSqlServerLinkedService",
            "type": "LinkedServiceReference"
        }
    },
    "name": "SqlServerDataset"
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$sourceSqlServerDatasetDefiniton | Out-File c:\SqlServerDataset.json

# Create an Azure Blob dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File "c:\SqlServerDataset.json"

# Create a dataset for sink Azure Blob Storage

## JSON definition of the dataset
$sinkBlobDatasetDefiniton = @"
{
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "$blobFolderPath",
            "format": {
                "type": "TextFormat"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    },
    "name": "AzureBlobDataset"
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$sinkBlobDatasetDefiniton | Out-File c:\AzureBlobDataset.json

## Create the Azure Blob dataset
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File "c:\AzureBlobDataset.json"


# Create a pipeline in the data factory

## JSON definition of the pipeline
$pipelineDefinition = @"
{
   "name": "$pipelineName",
    "properties": {
        "activities": [       
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource"
                    },
                    "sink": {
                        "type":"BlobSink"
                    }
                },
                "name": "CopySqlServerToAzureBlobActivity",
                "inputs": [
                    {
                        "referenceName": "SqlServerDataset",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "AzureBlobDataset",
                        "type": "DatasetReference"
                    }
                ]
            }
        ]
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryPipeline command. 
$pipelineDefinition | Out-File c:\SqlServerToBlobPipeline.json

## Create a pipeline in the data factory
Set-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$pipelineName" -File "c:\SqlServerToBlobPipeline.json"


# start the pipeline run
$runId = Invoke-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName

# Check the pipeline run status until it finishes the copy operation
while ($True) {
    $result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        Start-Sleep -Seconds 30
    }
    else {
        Write-Host "Pipeline $pipelineName run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
}

# Get the activity run details 
    $result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName `
        -PipelineRunId $runId `
        -RunStartedAfter (Get-Date).AddMinutes(-10) `
        -RunStartedBefore (Get-Date).AddMinutes(10) `
        -ErrorAction Stop

    $result

    if ($result.Status -eq "Succeeded") {`
        $result.Output -join "`r`n"`
    }`
    else {`
        $result.Error -join "`r`n"`
    }

# To remove the data factory from the resource gorup
# Remove-AzDataFactory -Name $dataFactoryName -ResourceGroupName $resourceGroupName
# 
# To remove the whole resource group
# Remove-AzResourceGroup  -Name $resourceGroupName
```


## <a name="clean-up-deployment"></a>Очистка развертывания

После выполнения примера скрипта можно удалить группу ресурсов и все связанные с ней ресурсы, выполнив следующую команду.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Чтобы удалить фабрику данных из группы ресурсов, выполните следующую команду: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Команда | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Создали фабрику данных. |
| [Новый AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Шифрует учетные данные в связанной службе и создает новое определение связанной службы с зашифрованными учетными данными. 
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Создает в этой фабрике данных связанную службу. Связанная служба вычисляет или привязывает хранилище данных к фабрике данных. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Создает набор данных в фабрике данных. Набор данных представляет ввод или вывод для действия в конвейере. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Создает конвейер в фабрике данных. Конвейер содержит одно или несколько действий, выполняющих определенную операцию. В этом конвейере действие копирования копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. |
| [Вызвать AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Создает выполнение для конвейера. Другими словами, запускает конвейер. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Получает сведения о выполнении действия в конвейере. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для Azure Data Factory приведены [здесь](../samples-powershell.md).
