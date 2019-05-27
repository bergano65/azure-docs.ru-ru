---
title: Скрипт PowerShell. Пошаговое копирование данных с помощью Фабрики данных Azure | Документация Майкрософт
description: Этот сценарий PowerShell демонстрирует, как с помощью фабрики данных Azure выполнять добавочное копирование данных из базы данных SQL Azure в хранилище BLOB-объектов Azure.
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
ms.openlocfilehash: 5ae6d6ed06aa5734dc601e6e72ba55ec8ddf7bcf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160625"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>Сценарий PowerShell для пошаговой загрузки данных с помощью фабрики данных Azure
Этот пример сценария PowerShell загружает в приемник только новые или обновленные записи из исходного хранилища данных после первоначального полного копирования данных из источника в приемник.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Перечень компонентов, необходимых для запуска этого примера, см. в [руководстве по добавочному копированию](../tutorial-incremental-copy-powershell.md#prerequisites). 

## <a name="sample-script"></a>Пример скрипта

> [!IMPORTANT]
> Этот скрипт создает JSON-файлы, определяющие сущности фабрики данных (связанную службу, набор данных и конвейер) на жестком диске в папке c:\.

```powershell
# Set variables with your own values
$resourceGroupName = "<azure resource group name>" # group will be created if it does not exist already
$dataFactoryName = "<data factory name>" # must be globally unique
$dataFactoryRegion = "East US" 
$storageAccountName = "<Az.Storage account name>"
$storageAccountKey = "<Az.Storage account key>"
$azureSqlServer = "<azure sql server name>"
$azureSqlDatabase = "<azure sql database name>"
$azureSqlUser = "<azure sql server - user name>"
$azureSqlPassword = "<azure sql server - user password>"
$outputBlobFolderPath = "<azure blob container>/<folder>" # output folder where the records are copied. 

$azureStorageLinkedServiceName = "AzureStorageLinkedService"
$azureSqlDatabaseLinkedServiceName = "AzureSQLDatabaseLinkedService"
$sourceDatasetName = "SourceDataset"
$sinkDatasetName = "SinkDataset"
$sourceSqlTableName = "datasource"
$watermarkDatasetName = "WatermarkDataset"
$pipelineName = "IncrementalCopyPipeline"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $dataFactoryRegion


# Create a data factory
$df = Set-AzDataFactory -ResourceGroupName $resourceGroupName -Location $dataFactoryRegion -Name $dataFactoryName 

# Create an Az.Storage linked service in the data factory

## JSON definition of the linked service. 
$storageLinkedServiceDefinition = @"
{
    "name": "$azureStorageLinkedServiceName",
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
$storageLinkedServiceDefinition | Out-File c:\$azureStorageLinkedServiceName.json

## Creates a linked service in the data factory
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$azureStorageLinkedServiceName" -File c:\$azureStorageLinkedServiceName.json

## JSON definition of the linked service. 
$azureSQLDatabaseLinkedServiceDefinition = @"
{
    "name": "$azureSqlDatabaseLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "value": "Server = tcp:$azureSqlServer.database.windows.net,1433;Initial Catalog=$azureSqlDatabase; Persist Security Info=False; User ID=$azureSqlUser; Password=$azureSqlPassword; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                "type": "SecureString"
            }
        }
    }
}
"@

## IMPORTANT: stores the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$azureSQLDatabaseLinkedServiceDefinition | Out-File c:\$azureSqlDatabaseLinkedServiceName.json

## Creates a linked service in the data factory
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$azureSqlDatabaseLinkedServiceName" -File c:\$azureSqlDatabaseLinkedServiceName.json

# Create an Azure SQL dataset in the data factory

## JSON definition of the dataset
$sourceDataset = @"
{
    "name": "$sourceDatasetName",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties": {
            "tableName": "$sourceSqlTableName"
        },
        "linkedServiceName": {
            "referenceName": "$azureSqlDatabaseLinkedServiceName",
            "type": "LinkedServiceReference"
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$sourceDataset | Out-File c:\$sourceDatasetName.json

## Create a dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$sourceDatasetName" -File "c:\$sourceDatasetName.json"


# Create an Azure Blob dataset in the data factory

## JSON definition of the dataset
$sinkDataset = @"
{
    "name": "$sinkDatasetName",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "$outputBlobFolderPath",
            "format": {
                "type": "TextFormat"
            }
        },
        "linkedServiceName": {
            "referenceName": "$azureStorageLinkedServiceName",
            "type": "LinkedServiceReference"
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$sinkDataset | Out-File c:\$sinkDatasetName.json

## Create a dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$sinkDatasetName" -File "c:\$sinkDatasetName.json"

## JSON definition of the dataset
$watermarkDataset = @"
{
    "name": "$watermarkDatasetName",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties": {
            "tableName": "watermarktable"
        },
        "linkedServiceName": {
            "referenceName": "$azureSqlDatabaseLinkedServiceName",
            "type": "LinkedServiceReference"
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$watermarkDataset | Out-File c:\$watermarkDatasetName.json

## Create a dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$watermarkDatasetName" -File "c:\$watermarkDatasetName.json"

# Create a pipeline in the data factory

## JSON definition of the pipeline
$pipelineDefinition = @"
{
    "name": "$pipelineName",
    "properties": {
        "activities": [
            {
                "name": "LookupWaterMarkActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                    "type": "SqlSource",
                    "sqlReaderQuery": "select * from watermarktable"
                    },

                    "dataset": {
                    "referenceName": "$watermarkDatasetName",
                    "type": "DatasetReference"
                    }
                }
            },
            {
                "name": "LookupMaxValuefromSourceActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "SELECT MAX(LastModifytime) as NewWatermarkvalue FROM dbo.datasource"
                    },

                    "dataset": {
                    "referenceName": "$sourceDatasetName",
                    "type": "DatasetReference"
                    }
                }
            },

            {
                "name": "IncrementalCopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from dbo.datasource where LastModifytime > '@{activity('LookupWaterMarkActivity').output.WatermarkValue}' and LastModifytime <= '@{activity('LookupMaxValuefromSourceActivity').output.NewWatermarkvalue}'"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "dependsOn": [
                    {
                        "activity": "LookupMaxValuefromSourceActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    },
                    {
                        "activity": "LookupWaterMarkActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],

                "inputs": [
                    {
                        "referenceName": "$sourceDatasetName",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "$sinkDatasetName",
                        "type": "DatasetReference"
                    }
                ]
            },

            {
                "name": "StoredProceduretoWriteWatermarkActivity",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {

                    "storedProcedureName": "sp_write_watermark",
                    "storedProcedureParameters": {
                        "LastModifiedtime": {"value": "@{activity('LookupMaxValuefromSourceActivity').output.NewWatermarkvalue}", "type": "datetime" },
                        "TableName":  { "value":"@{activity('LookupWaterMarkActivity').output.TableName}", "type":"String"}
                    }
                },

                "linkedServiceName": {
                    "referenceName": "$azureSqlDatabaseLinkedServiceName",
                    "type": "LinkedServiceReference"
                },

                "dependsOn": [
                    {
                        "activity": "IncrementalCopyActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ]
            }
        ],

    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryPipeline command. 
$pipelineDefinition | Out-File c:\$pipelineName.json

## Create a pipeline in the data factory
Set-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$pipelineName" -File "c:\$pipelineName.json"


$RunId = Invoke-AzDataFactoryPipeline -PipelineName "$pipelineName" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName

# Check the pipeline run status until it finishes the copy operation
Start-Sleep -Seconds 30
while ($True) {
    $result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        Start-Sleep -Seconds 30
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
}


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

# INSERT INTO datasource
# VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

# INSERT INTO datasource
# VALUES (7, 'newdata','9/7/2017 9:01:00 AM')

# Inovke the pipeline and see that only new records are copied to the destination. 


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
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Создает в этой фабрике данных связанную службу. Связанная служба вычисляет или привязывает хранилище данных к фабрике данных. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Создает набор данных в фабрике данных. Набор данных представляет ввод или вывод для действия в конвейере. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Создает конвейер в фабрике данных. Конвейер содержит одно или несколько действий, выполняющих определенную операцию. В этом конвейере действие копирования копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. |
| [Вызвать AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Создает выполнение для конвейера. Другими словами, запускает конвейер. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Получает сведения о выполнении действия в конвейере. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Удаляет группу ресурсов со всеми вложенными ресурсами. |
|||

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев Azure Data Factory PowerShell приведены [здесь](../samples-powershell.md).
