---
title: Вызов пакета SSIS из фабрики данных Azure с помощью действия хранимой процедуры | Документация Майкрософт
description: В этой статье рассматривается вызов пакета SQL Server Integration Services (SSIS) из конвейера базы данных Azure с помощью действия хранимой процедуры.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 030617d3afd73c68793ca0a1d6185264c92b791f
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839900"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Вызов пакета SSIS с помощью действия хранимой процедуры в фабрике данных Azure
В этой статье описывается, как вызвать пакет SSIS из конвейера фабрики данных Azure, используя действие хранимой процедуры. 

> [!NOTE]
> В этой статье рассматривается служба "Фабрика данных Azure" версии 1. Если вы используете текущую версию службы "Фабрика данных", см. руководство по [вызову пакетов SSIS с помощью действия хранимой процедуры в службе "Фабрика данных" версии 2](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>предварительные требования

### <a name="azure-sql-database"></a>База данных SQL Azure 
В этих пошаговых инструкциях используется база данных SQL Azure, в которой размещен каталог SSIS. Вы также можете использовать Управляемый экземпляр Базы данных SQL.

### <a name="create-an-azure-ssis-integration-runtime"></a>Создание среды выполнения интеграции Azure SSIS.
Создайте среду выполнения интеграции Azure SSIS, если у вас ее нет. Для этого выполните пошаговую инструкцию из статьи [Подготовка Integration Runtime Azure–SSIS в Фабрике данных Azure](../tutorial-create-azure-ssis-runtime-portal.md). Для создания среды выполнения интеграции Azure SSIS нельзя использовать службу "Фабрика данных" версии 1. 

## <a name="azure-powershell"></a>Azure PowerShell
В этом разделе с помощью Azure PowerShell вы создадите конвейер фабрики данных с действием хранимой процедуры, которое вызывает пакет SSIS.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Чтобы установить модули Azure PowerShell, выполните инструкции из статьи [Установка и настройка Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Создание фабрики данных
В следующей процедуре представлены шаги для создания фабрики данных. Вы создадите конвейер с действием хранимой процедуры в фабрике данных. Действие хранимой процедуры выполняет хранимую процедуру в базе данных SSISDB для запуска вашего пакета SSIS.

1. Определите переменную для имени группы ресурсов, которую в дальнейшем можно будет использовать в командах PowerShell. Скопируйте текст следующей команды в PowerShell, укажите имя [группы ресурсов Azure](../../azure-resource-manager/resource-group-overview.md) в двойных кавычках, а затем выполните команду. Например, `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Если группа ресурсов уже существует, вы можете не перезаписывать ее. Назначьте переменной `$ResourceGroupName` другое значение и еще раз выполните команду.
2. Чтобы создать группу ресурсов Azure, выполните следующую команду: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Если группа ресурсов уже существует, вы можете не перезаписывать ее. Назначьте переменной `$ResourceGroupName` другое значение и еще раз выполните команду. 
3. Определите переменную для имени фабрики данных. 

    > [!IMPORTANT]
    >  Измените имя фабрики данных, чтобы оно было глобально уникальным. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Чтобы создать фабрику данных, выполните следующую команду **New AzDataFactory** командлет, используя свойства Location и ResourceGroupName из переменной $ResGrp: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Обратите внимание на следующие моменты.

* Имя фабрики данных Azure должно быть глобально уникальным. Если появляется следующая ошибка, измените имя и повторите попытку.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Чтобы создать экземпляры фабрики данных, нужно назначить учетной записи пользователя, используемой для входа в Azure, роль **участника**, **владельца** либо **администратора** подписки Azure.

### <a name="create-an-azure-sql-database-linked-service"></a>Создание связанной службы Базы данных SQL Azure
Создайте связанную службу, которая свяжет базу данных SQL Azure, содержащую каталог SSIS, с вашей фабрикой данных. Фабрика данных использует информацию связанной службы для подключения к базе данных SSISDB и выполняет хранимую процедуру для запуска пакета SSIS. 

1. В папке **C:\ADF\RunSSISPackage** создайте файл JSON с именем **AzureSqlDatabaseLinkedService.json** и следующим содержимым: 

    > [!IMPORTANT]
    > Перед сохранением файла замените заполнители &lt;servername&gt;, &lt;username&gt;@&lt;servername&gt; и &lt;password&gt; значениями для используемой базы данных SQL Azure.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. В **Azure PowerShell** перейдите в папку **C:\ADF\RunSSISPackage**.
3. Чтобы создать связанную службу, выполните командлет **New-AzDataFactoryLinkedService**: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Создание выходного набора данных
Этот пустой выходной набор данных используется для выполнения расписания конвейера. Обратите внимание, что частота составляет час, а интервал — 1. Это значит, что конвейер выполняется раз в час на протяжении всего периода между временем начала и окончания конвейера. 

1. Создайте файл OutputDataset.json со следующим содержимым: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Запустите **New AzDataFactoryDataset** командлет, чтобы создать набор данных. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Создание конвейера с действием хранимой процедуры 
На этом этапе создается конвейер с действием хранимой процедуры. Это действие вызывает хранимую процедуру sp_executesql для запуска пакета SSIS. 

1. Создайте файл JSON с именем **MyPipeline.json** в папке **C:\ADF\RunSSISPackage** со следующим содержимым:

    > [!IMPORTANT]
    > Прежде чем сохранять файл, замените заполнители &lt;folder name&gt;, &lt;project name&gt;, &lt;package name&gt; именами папки, проекта и пакета в каталоге SSIS соответственно.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Чтобы создать конвейер **RunSSISPackagePipeline**, запустите **New AzDataFactoryPipeline** командлета.

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Мониторинг конвейера

1. Запустите **Get-AzDataFactorySlice** для получения сведений обо всех срезах выходного набора данных **, которая является выходной таблицей конвейера.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Обратите внимание, здесь указывается то же значение StartDateTime, что и в JSON конвейера. 
1. Выполните командлет **Get-AzDataFactoryRun**, чтобы получить сведения о действиях, выполняемых для конкретного среза.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Вы можете выполнять этот командлет до тех пор, пока не увидите срез с состоянием **Готово** или **Сбой**. 

    Вы можете запустить следующий запрос к базе данных SSISDB на своем сервере SQL Server Azure, чтобы убедиться, что пакет выполнен. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Следующие шаги
См дополнительные сведения о [действии хранимой процедуры](data-factory-stored-proc-activity.md).

