---
title: Настройка Azure-SSIS Integration Runtime для отработки отказа базы данных SQL
description: В этой статье описывается настройка Azure-SSIS Integration Runtime с помощью георепликации базы данных SQL Azure и отработка отказа для базы данных SSISDB
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/14/2018
ms.openlocfilehash: 92f7d25a9c19409b220b6a71fba87da91e51a415
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928496"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Настройка Azure-SSIS Integration Runtime с помощью георепликации базы данных SQL Azure и отработка отказа

В этой статье описывается настройка Azure-SSIS Integration Runtime с помощью георепликации базы данных SQL Azure для базы данных SSISDB. В процессе отработки отказа работоспособность Azure-SSIS Integration Runtime можно отслеживать через базу данных-получатель.

Дополнительные сведения о георепликации и отработке отказа базы данных SQL см. в статье [Обзор. Активная георепликация и группы автоматической отработки отказа](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Сценарий 1. Azure-SSIS IR указывает на конечную точку прослушивателя записи

### <a name="conditions"></a>Условия

Данный раздел применяется при выполнении следующих условий:

- Azure-SSIS IR указывает на конечную точку прослушивателя записи группы отработки отказа.

  И

- В сервере Базы данных SQL *не* настроено правило конечной точки службы для виртуальной сети.

### <a name="solution"></a>Решение

Когда происходит отработка отказа, она является прозрачной для Azure-SSIS IR. Azure-SSIS IR автоматически подключается к новой первичной точке группы отработки отказа.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Сценарий 2. Azure-SSIS IR указывает на конечную точку основного сервера

### <a name="conditions"></a>Условия

Данный раздел применяется при выполнении одного из следующих условий:

- Azure-SSIS IR указывает на конечную точку основного сервера группы отработки отказа. Эта конечная точка изменяется при отработке отказа.

  Или

- В сервере Базы данных SQL Azure настроено правило конечной точки службы для виртуальной сети.

  Или

- Сервер базы данных является управляемым экземпляром базы данных SQL, настроенным с помощью виртуальной сети.

### <a name="solution"></a>Решение

При отработке отказа необходимо выполнить следующие действия:

1. Остановите среду выполнения интеграции Azure SSIS.

2. Для основной конечной точки и виртуальной сети в новом регионе необходимо перенастроить среду выполнения интеграции.

3. Перезапустите среду выполнения интеграции.

В следующих разделах данные шаги описаны более подробно.

### <a name="prerequisites"></a>Технические условия

- Убедитесь, что вы активировали аварийное восстановление для сервера Базы данных SQL Azure на случай сбоя в работе сервера в то же время. Дополнительные сведения см. в статье [Обзор обеспечения непрерывности бизнес-процессов с помощью Базы данных SQL Azure](../sql-database/sql-database-business-continuity.md).

- Если вы используете виртуальную сеть в текущем регионе, нужно использовать другую виртуальную сеть в новом регионе для подключения среды выполнения интеграции Azure-SSIS. Дополнительные сведения см. в статье [Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети](join-azure-ssis-integration-runtime-virtual-network.md).

- Если вы используете выборочную установку, может потребоваться подготовить еще один универсальный код ресурса SAS для контейнера больших двоичных объектов, в котором хранится сценарий выборочной установки и связанные с ним файлы, чтобы он оставался доступным во время сбоя. Дополнительные сведения см. в статье [Пользовательская установка для среды выполнения интеграции Azure–SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Действия

Выполните следующие шаги, чтобы остановить среду выполнения интеграции Azure-SSIS, переключить ее в новый регион и запустить снова.

1. Остановите среду выполнения интеграции в исходном регионе.

2. Чтобы обновить настройки в среде выполнения интеграции, вызовите следующую команду в PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Дополнительные сведения об этой команде PowerShell см. в статье [Создание среды выполнения интеграции Azure SSIS в службе "Фабрика данных Azure"](create-azure-ssis-integration-runtime.md).

3. Запустите среду выполнения интеграции снова.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Сценарий 3. присоединение существующего каталога SSISDB (каталог SSIS) к новому Azure-SSIS IR

В случае сбоя ADF или Azure-SSIS IR в текущем регионе можно сделать SSISDB невозможной работать с новым Azure-SSIS IR в новом регионе.

### <a name="prerequisites"></a>Технические условия

- Если вы используете виртуальную сеть в текущем регионе, нужно использовать другую виртуальную сеть в новом регионе для подключения среды выполнения интеграции Azure-SSIS. Дополнительные сведения см. в статье [Присоединение среды выполнения интеграции Azure SSIS к виртуальной сети](join-azure-ssis-integration-runtime-virtual-network.md).

- Если вы используете выборочную установку, может потребоваться подготовить еще один универсальный код ресурса SAS для контейнера больших двоичных объектов, в котором хранится сценарий выборочной установки и связанные с ним файлы, чтобы он оставался доступным во время сбоя. Дополнительные сведения см. в статье [Пользовательская установка для среды выполнения интеграции Azure–SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Действия

Выполните следующие шаги, чтобы остановить среду выполнения интеграции Azure-SSIS, переключить ее в новый регион и запустить снова.

1. Выполните хранимую процедуру, чтобы прикрепить SSISDB к **\<new_data_factory_name\>** или **\<new_integration_runtime_name** \>.
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Создайте новую фабрику данных с именем **\<new_data_factory_name\>** в новом регионе. Дополнительные сведения см. в разделе Создание фабрики данных.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Дополнительные сведения об этой команде PowerShell см. в статье [Создание фабрики данных Azure с помощью PowerShell](quickstart-create-data-factory-powershell.md) .

3. Создайте новый Azure-SSIS IR с именем **\<new_integration_runtime_name\>** в новом регионе, используя Azure PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Дополнительные сведения об этой команде PowerShell см. в статье [Создание среды выполнения интеграции Azure SSIS в службе "Фабрика данных Azure"](create-azure-ssis-integration-runtime.md).

4. Запустите среду выполнения интеграции снова.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь со следующими параметрами конфигурации для среды выполнения интеграции Azure-SSIS:

- [Настройка среды выполнения интеграции Azure-SSIS для высокой производительности](configure-azure-ssis-integration-runtime-performance.md);

- [Пользовательская установка для среды выполнения интеграции Azure–SSIS](how-to-configure-azure-ssis-ir-custom-setup.md);

- [Подготовка выпуска Enterprise Edition для среды выполнения интеграции Azure Integration Services](how-to-configure-azure-ssis-ir-enterprise-edition.md).
