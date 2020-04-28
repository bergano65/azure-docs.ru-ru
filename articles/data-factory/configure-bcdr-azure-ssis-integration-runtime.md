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
ms.date: 04/09/2020
ms.openlocfilehash: 39d55d4372f03a1625bb04d8377ed6533401e281
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188728"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Настройка Azure-SSIS Integration Runtime с помощью георепликации базы данных SQL Azure и отработка отказа

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье описывается настройка Azure-SSIS Integration Runtime с помощью георепликации базы данных SQL Azure для базы данных SSISDB. В процессе отработки отказа работоспособность Azure-SSIS Integration Runtime можно отслеживать через базу данных-получатель.

Дополнительные сведения о георепликации и отработке отказа базы данных SQL см. в статье [Обзор. Активная георепликация и группы автоматической отработки отказа](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Azure-SSIS IR отработки отказа с Управляемый экземпляр Базы данных SQL Azure

### <a name="prerequisites"></a>Предварительные требования
1. Выполните приведенную ниже команду в SSISDB на первичном экземпляре. Этот шаг добавляет новый пароль шифрования.
```sql
  ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
```

2. Создайте группу отработки отказа на Управляемый экземпляр Базы данных SQL Azure.

3. Запустите **sp_control_dbmasterkey_password** на дополнительном экземпляре, используя новый пароль шифрования.
```sql
  EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
    @password = N'<password>', @action = N'add';  
  GO
```

### <a name="solution"></a>Решение
Когда происходит отработка отказа, если вы хотите использовать существующую Azure-SSIS IR в основном регионе:
1. Останавливает Azure-SSIS IR в основном регионе.

2. Измените Azure-SSIS IR с помощью сведений о новом регионе, конечной точке и виртуальной сети для экземпляра-получателя.

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
```

3. Перезапустите Azure-SSIS IR.

4. Измените имя сервера в **ConnectionManager** для пакетов служб SSIS с помощью имени сервера вторичного экземпляра, затем повторно разверните эти пакеты и запустите.

Если вы хотите подготавливать новый Azure-SSIS IR в дополнительном регионе:
> [!NOTE]
> Шаг 4 (создание IR) необходимо выполнить с помощью PowerShell. Портал Azure сообщит об ошибке с уведомлением о том, что SSISDB уже существует.
1. Останавливает Azure-SSIS IR в основном регионе.

2. Выполните хранимую процедуру для обновления метаданных в SSISDB, чтобы принимать подключения от ** \<new_data_factory_name\> ** и ** \<new_integration_runtime_name\>**.
   
```SQL
  EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
```

3. Создайте новую фабрику данных с ** \<именем\> new_data_factory_name** в новом регионе. Дополнительные сведения см. в разделе Создание фабрики данных.

```powershell
  Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
```
  Дополнительные сведения об этой команде PowerShell см. в статье [Создание фабрики данных Azure с помощью PowerShell](quickstart-create-data-factory-powershell.md) .

4. Создайте новый Azure-SSIS IR с именем ** \<new_integration_runtime_name\> ** в новом регионе, используя Azure PowerShell.

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

5. Измените имя сервера в **ConnectionManager** для пакетов служб SSIS с помощью имени сервера вторичного экземпляра, затем повторно разверните эти пакеты и запустите.



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Azure-SSIS IR отработки отказа с помощью базы данных SQL Azure

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Сценарий 1. Azure-SSIS IR указывает на конечную точку прослушивателя записи

#### <a name="conditions"></a>Условия

Данный раздел применяется при выполнении следующих условий:

- Azure-SSIS IR указывает на конечную точку прослушивателя записи группы отработки отказа.

  AND

- В сервере Базы данных SQL *не* настроено правило конечной точки службы для виртуальной сети.

#### <a name="solution"></a>Решение

Когда происходит отработка отказа, она является прозрачной для Azure-SSIS IR. Azure-SSIS IR автоматически подключается к новой первичной точке группы отработки отказа.


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Сценарий 2. Azure-SSIS IR указывает на конечную точку основного сервера

#### <a name="conditions"></a>Условия

Данный раздел применяется при выполнении одного из следующих условий:

- Azure-SSIS IR указывает на конечную точку основного сервера группы отработки отказа. Эта конечная точка изменяется при отработке отказа.

  ИЛИ

- В сервере Базы данных SQL Azure настроено правило конечной точки службы для виртуальной сети.


#### <a name="solution"></a>Решение

1. Останавливает Azure-SSIS IR в основном регионе.

2. Изменение Azure-SSIS IR с новыми сведениями о регионе, конечной точке и виртуальной сети для экземпляра-получателя.

```powershell
  Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
```

3. Перезапустите Azure-SSIS IR.

4. Измените имя сервера в **ConnectionManager** для пакетов служб SSIS с помощью имени сервера вторичного экземпляра, затем повторно разверните эти пакеты и запустите.


### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Сценарий 3. присоединение существующего каталога SSISDB (каталог SSIS) к новому Azure-SSIS IR

В случае сбоя ADF или Azure-SSIS IR в текущем регионе можно сделать SSISDB невозможной работать с новым Azure-SSIS IR в новом регионе.

#### <a name="solution"></a>Решение

> [!NOTE]
> Шаг 4 (создание IR) необходимо выполнить с помощью PowerShell. Портал Azure сообщит об ошибке с уведомлением о том, что SSISDB уже существует.

1. Останавливает Azure-SSIS IR в основном регионе.

2. Выполните хранимую процедуру для обновления метаданных в SSISDB, чтобы принимать подключения от ** \<new_data_factory_name\> ** и ** \<new_integration_runtime_name\>**.
   
```SQL
  EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
```

3. Создайте новую фабрику данных с ** \<именем\> new_data_factory_name** в новом регионе. Дополнительные сведения см. в разделе Создание фабрики данных.

```powershell
  Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
```
  Дополнительные сведения об этой команде PowerShell см. в статье [Создание фабрики данных Azure с помощью PowerShell](quickstart-create-data-factory-powershell.md) .

4. Создайте новый Azure-SSIS IR с именем ** \<new_integration_runtime_name\> ** в новом регионе, используя Azure PowerShell.

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

5. Измените имя сервера в **ConnectionManager** для пакетов служб SSIS с помощью имени сервера вторичного экземпляра, затем повторно разверните эти пакеты и запустите.


## <a name="next-steps"></a>Следующие шаги

Ознакомьтесь со следующими параметрами конфигурации для среды выполнения интеграции Azure-SSIS:

- [Настройка среды выполнения интеграции Azure-SSIS для высокой производительности](configure-azure-ssis-integration-runtime-performance.md);

- [Пользовательская установка для среды выполнения интеграции Azure–SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Подготовка выпуска Enterprise Edition для среды выполнения интеграции Azure Integration Services](how-to-configure-azure-ssis-ir-enterprise-edition.md).
