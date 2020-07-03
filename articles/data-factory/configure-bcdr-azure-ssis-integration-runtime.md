---
title: Настройка среды выполнения интеграции Azure SSIS для отработки отказа базы данных SQL
description: В этой статье описывается, как настроить среду выполнения интеграции Azure-SSIS с георепликацией и отработкой отказа базы данных SQL Azure для базы данных SSISDB.
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
ms.date: 04/09/2020
ms.openlocfilehash: 795247cd0d6adfd27115b73c1d0de02e6810d670
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201142"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>Настройка среды выполнения интеграции Azure SSIS с георепликацией и отработкой отказа базы данных SQL

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

В этой статье описывается настройка среды выполнения интеграции Azure-SSIS (IR) с помощью георепликации базы данных SQL Azure для базы данных SSISDB. В процессе отработки отказа работоспособность Azure-SSIS Integration Runtime можно отслеживать через базу данных-получатель.

Дополнительные сведения о георепликации и отработке отказа базы данных SQL см. в статье [Обзор. Активная георепликация и группы автоматической отработки отказа](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-database-managed-instance"></a>Azure-SSIS IR отработки отказа с помощью управляемого экземпляра базы данных SQL

### <a name="prerequisites"></a>Предварительные требования

Управляемый экземпляр базы данных SQL Azure использует *главный ключ базы данных (DMK)* , чтобы защитить данные, учетные данные и сведения о соединении, хранящиеся в базе данных. Чтобы включить автоматическую расшифровку DMK, копия ключа шифруется с помощью *главного ключа сервера (SMK)*. 

SMK не реплицируется в группе отработки отказа. После отработки отказа необходимо добавить пароль как на первичном, так и на вторичном экземплярах для расшифровки DMK.

1. Выполните следующую команду для SSISDB на основном экземпляре. На этом шаге добавляется новый пароль шифрования.

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Создайте группу отработки отказа в управляемом экземпляре базы данных SQL Azure.

3. Запустите **sp_control_dbmasterkey_password** на дополнительном экземпляре, используя новый пароль шифрования.

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Сценарий 1. Azure-SSIS IR указывает на конечную точку прослушивателя для чтения и записи

Если вы хотите, чтобы Azure-SSIS IR указывала на конечную точку прослушивателя для чтения и записи, сначала необходимо указать конечную точку сервера-источника. После помещения SSISDB в группу отработки отказа можно перейти на конечную точку прослушивателя для чтения и записи и перезапустить Azure-SSIS IR.

#### <a name="solution"></a>Решение

В случае отработки отказа выполните следующие действия.

1. Останавливает Azure-SSIS IR в основном регионе.

2. Измените Azure-SSIS IR с помощью нового региона, виртуальной сети и URI подписанного URL-адрес (SAS) для пользовательской установки на вторичном экземпляре. Так как Azure-SSIS IR указывает на прослушиватель чтения и записи, а конечная точка прозрачна для Azure-SSIS IR, нет необходимости изменять конечную точку.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Перезапустите Azure-SSIS IR.

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Сценарий 2. Azure-SSIS IR указывает на конечную точку сервера-источника

Этот сценарий подходит, если Azure-SSIS IR указывает на конечную точку сервера-источника.

#### <a name="solution"></a>Решение

В случае отработки отказа выполните следующие действия.

1. Останавливает Azure-SSIS IR в основном регионе.

2. Измените Azure-SSIS IR, указав сведения о новом регионе, конечной точке и виртуальной сети для вторичного экземпляра.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Перезапустите Azure-SSIS IR.

### <a name="scenario-3-azure-ssis-ir-is-pointing-to-a-public-endpoint-of-a-sql-database-managed-instance"></a>Сценарий 3. Azure-SSIS IR указывает на общедоступную конечную точку управляемого экземпляра базы данных SQL

Этот сценарий подходит, если Azure-SSIS IR указывает на общедоступную конечную точку управляемого экземпляра базы данных SQL Azure и не присоединяется к виртуальной сети. Единственное отличие от сценария 2 в том, что вам не нужно изменять сведения о виртуальной сети для Azure-SSIS IR после отработки отказа.

#### <a name="solution"></a>Решение

В случае отработки отказа выполните следующие действия.

1. Останавливает Azure-SSIS IR в основном регионе.

2. Измените Azure-SSIS IR, указав новый регион и сведения о конечной точке для вторичного экземпляра.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Перезапустите Azure-SSIS IR.

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>Сценарий 4. присоединение существующего экземпляра SSISDB (каталога служб SSIS) к новой Azure-SSIS IR

Этот сценарий подходит, если вы хотите, чтобы база данных SSISDB работала с новым Azure-SSIS IR в новом регионе, когда в текущем регионе происходит Azure-SSIS IR аварии.

#### <a name="solution"></a>Решение

В случае отработки отказа выполните следующие действия.

> [!NOTE]
> Используйте PowerShell для шага 4 (создание IR). В противном случае портал Azure сообщит об ошибке с текстом SSISDB уже существует.

1. Останавливает Azure-SSIS IR в основном регионе.

2. Выполните хранимую процедуру для обновления метаданных в SSISDB, чтобы принимать подключения от ** \< new_data_factory_name \> ** и ** \< new_integration_runtime_name \> **.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Создайте новую фабрику данных с именем ** \< new_data_factory_name \> ** в новом регионе.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    Дополнительные сведения об этой команде PowerShell см. в статье [Создание фабрики данных Azure с помощью PowerShell](quickstart-create-data-factory-powershell.md).

4. Создайте новый Azure-SSIS IR с именем ** \< new_integration_runtime_name \> ** в новом регионе с помощью Azure PowerShell.

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

    Дополнительные сведения об этой команде PowerShell см. [в статье Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure](create-azure-ssis-integration-runtime.md).



## <a name="azure-ssis-ir-failover-with-sql-database"></a>Azure-SSIS IR отработки отказа с помощью базы данных SQL

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Сценарий 1. Azure-SSIS IR указывает на конечную точку прослушивателя для чтения и записи

Этот сценарий подходит в следующих случаях:

- Azure-SSIS IR указывает на конечную точку прослушивателя для чтения и записи группы отработки отказа.
- На сервере базы данных SQL *не* настроено правило для конечной точки службы виртуальной сети.

Если вы хотите, чтобы Azure-SSIS IR указывала на конечную точку прослушивателя для чтения и записи, сначала необходимо указать конечную точку сервера-источника. После помещения SSISDB в группу отработки отказа можно перейти на конечную точку прослушивателя для чтения и записи и перезапустить Azure-SSIS IR.

#### <a name="solution"></a>Решение

Когда происходит отработка отказа, она прозрачна для Azure-SSIS IR. Azure-SSIS IR автоматически подключается к новой первичной точке группы отработки отказа. 

Если вы хотите обновить регион или другую информацию в Azure-SSIS IR, можно ее отключить, изменить и перезапустить.


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>Сценарий 2. Azure-SSIS IR указывает на конечную точку сервера-источника

Этот сценарий подходит, если Azure-SSIS IR указывает на конечную точку сервера-источника.

#### <a name="solution"></a>Решение

В случае отработки отказа выполните следующие действия.

1. Останавливает Azure-SSIS IR в основном регионе.

2. Измените Azure-SSIS IR, указав сведения о новом регионе, конечной точке и виртуальной сети для вторичного экземпляра.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Перезапустите Azure-SSIS IR.

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Сценарий 3. присоединение существующего экземпляра SSISDB (каталога SSIS) к новой Azure-SSIS IR

Этот сценарий подходит, если вы хотите подготавливать новый Azure-SSIS IR в дополнительном регионе. Он также подходит, если вы хотите, чтобы база данных SSISDB продолжала работать с новым Azure-SSIS IR в новом регионе, когда в текущем регионе происходит Azure-SSIS IR аварии.

#### <a name="solution"></a>Решение

В случае отработки отказа выполните следующие действия.

> [!NOTE]
> Используйте PowerShell для шага 4 (создание IR). В противном случае портал Azure сообщит об ошибке с текстом SSISDB уже существует.

1. Останавливает Azure-SSIS IR в основном регионе.

2. Выполните хранимую процедуру для обновления метаданных в SSISDB, чтобы принимать подключения от ** \< new_data_factory_name \> ** и ** \< new_integration_runtime_name \> **.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Создайте новую фабрику данных с именем ** \< new_data_factory_name \> ** в новом регионе.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    Дополнительные сведения об этой команде PowerShell см. в статье [Создание фабрики данных Azure с помощью PowerShell](quickstart-create-data-factory-powershell.md).

4. Создайте новый Azure-SSIS IR с именем ** \< new_integration_runtime_name \> ** в новом регионе с помощью Azure PowerShell.

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

    Дополнительные сведения об этой команде PowerShell см. [в статье Создание среды выполнения интеграции Azure SSIS в фабрике данных Azure](create-azure-ssis-integration-runtime.md).


## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь со следующими параметрами конфигурации для среды выполнения интеграции Azure-SSIS:

- [Настройка среды выполнения интеграции Azure SSIS для обеспечения высокой производительности](configure-azure-ssis-integration-runtime-performance.md)

- [Пользовательская установка для среды выполнения интеграции Azure–SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Предоставление выпуска Enterprise Edition для среды выполнения интеграции Azure SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
