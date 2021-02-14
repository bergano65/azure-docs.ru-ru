---
title: Настройка среды выполнения интеграции Azure SSIS для отработки отказа базы данных SQL
description: В этой статье описывается, как настроить среду выполнения интеграции Azure-SSIS с георепликацией и отработкой отказа базы данных SQL Azure для базы данных SSISDB.
ms.service: data-factory
ms.devlang: powershell
author: swinarko
ms.author: sawinark
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/06/2020
ms.openlocfilehash: e12939d1003ce708889ca0b3dbc710096f9ee955
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364449"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>Настройка среды выполнения интеграции Azure SSIS с георепликацией и отработкой отказа базы данных SQL

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

В этой статье описывается настройка среды выполнения интеграции Azure-SSIS (IR) с помощью георепликации базы данных SQL Azure для базы данных SSISDB. В процессе отработки отказа работоспособность Azure-SSIS Integration Runtime можно отслеживать через базу данных-получатель.

Дополнительные сведения о георепликации и отработке отказа базы данных SQL см. в статье [Обзор. Активная георепликация и группы автоматической отработки отказа](../azure-sql/database/auto-failover-group-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-managed-instance"></a>Azure-SSIS IR отработки отказа с помощью Управляемый экземпляр SQL

### <a name="prerequisites"></a>Предварительные требования

Управляемый экземпляр Azure SQL использует *главный ключ базы данных (DMK)* , чтобы защитить данные, учетные данные и сведения о соединении, хранящиеся в базе данных. Чтобы включить автоматическую расшифровку DMK, копия ключа шифруется с помощью *главного ключа сервера (SMK)*. 

SMK не реплицируется в группе отработки отказа. После отработки отказа необходимо добавить пароль как на первичном, так и на вторичном экземплярах для расшифровки DMK.

1. Выполните следующую команду для SSISDB на основном экземпляре. На этом шаге добавляется новый пароль шифрования.

   ```sql
   ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
   ```

2. Создайте группу отработки отказа на Управляемый экземпляр SQL.

3. Запустите **sp_control_dbmasterkey_password** на дополнительном экземпляре, используя новый пароль шифрования.

   ```sql
   EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'<password>', @action = N'add';  
   GO
   ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>Сценарий 1. Azure-SSIS IR указывает на конечную точку прослушивателя для чтения и записи

Если вы хотите, чтобы Azure-SSIS IR указывала на конечную точку прослушивателя для чтения и записи, сначала необходимо указать конечную точку сервера-источника. После помещения SSISDB в группу отработки отказа можно остановить Azure-SSIS IR, изменить ее так, чтобы она указывала на конечную точку прослушивателя для чтения и записи, используя Azure PowerShell, и перезапустить ее.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -CatalogServerEndpoint "Azure SQL Managed Instance read/write listener endpoint"
```

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
      -CatalogServerEndpoint "Azure SQL Database endpoint" `
      -CatalogAdminCredential "Azure SQL Database admin credentials" `
      -VNetId "new VNet" `
      -Subnet "new subnet" `
      -SetupScriptContainerSasUri "new custom setup SAS URI"
   ```

3. Перезапустите Azure-SSIS IR.

### <a name="scenario-3-azure-ssis-ir-is-pointing-to-a-public-endpoint-of-a-sql-managed-instance"></a>Сценарий 3. Azure-SSIS IR указывает на общедоступную конечную точку SQL Управляемый экземпляр

Этот сценарий подходит, если Azure-SSIS IR указывает на общедоступную конечную точку Управляемый экземпляр SQL Azure и не присоединяется к виртуальной сети. Единственное отличие от сценария 2 в том, что вам не нужно изменять сведения о виртуальной сети для Azure-SSIS IR после отработки отказа.

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

2. Выполните хранимую процедуру для обновления метаданных в SSISDB, чтобы принимать подключения от **\<new_data_factory_name\>** и **\<new_integration_runtime_name\>** .
   
   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

3. Создайте новую фабрику данных с именем **\<new_data_factory_name\>** в новом регионе.

   ```powershell
   Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
      -Location "new region"`
      -Name "<new_data_factory_name>"
   ```
   
   Дополнительные сведения об этой команде PowerShell см. в статье [Создание фабрики данных Azure с помощью PowerShell](quickstart-create-data-factory-powershell.md).

4. Создайте новый Azure-SSIS IR с именем **\<new_integration_runtime_name\>** в новом регионе с помощью Azure PowerShell.

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

Если вы хотите, чтобы Azure-SSIS IR указывала на конечную точку прослушивателя для чтения и записи, сначала необходимо указать конечную точку сервера-источника. После помещения SSISDB в группу отработки отказа можно остановить Azure-SSIS IR, изменить ее так, чтобы она указывала на конечную точку прослушивателя для чтения и записи, используя Azure PowerShell, и перезапустить ее.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -CatalogServerEndpoint "Azure SQL Database read/write listener endpoint"
```

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
      -CatalogServerEndpoint "Azure SQL Database endpoint" `
      -CatalogAdminCredential "Azure SQL Database admin credentials" `
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

2. Выполните хранимую процедуру для обновления метаданных в SSISDB, чтобы принимать подключения от **\<new_data_factory_name\>** и **\<new_integration_runtime_name\>** .
   
   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

3. Создайте новую фабрику данных с именем **\<new_data_factory_name\>** в новом регионе.

   ```powershell
   Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
      -Location "new region"`
      -Name "<new_data_factory_name>"
   ```
   
   Дополнительные сведения об этой команде PowerShell см. в статье [Создание фабрики данных Azure с помощью PowerShell](quickstart-create-data-factory-powershell.md).

4. Создайте новый Azure-SSIS IR с именем **\<new_integration_runtime_name\>** в новом регионе с помощью Azure PowerShell.

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

## <a name="next-steps"></a>Следующие шаги

Ознакомьтесь со следующими параметрами конфигурации для среды выполнения интеграции Azure-SSIS:

- [Настройка среды выполнения интеграции Azure SSIS для обеспечения высокой производительности](configure-azure-ssis-integration-runtime-performance.md)

- [Пользовательская установка для среды выполнения интеграции Azure–SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Предоставление выпуска Enterprise Edition для среды выполнения интеграции Azure SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
