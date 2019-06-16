---
title: Настройка производительности для среды выполнения интеграции Azure-SSIS | Документация Microsoft
description: Узнайте, как настроить свойства среды выполнения интеграции Azure-SSIS для высокой производительности
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 42c69653a002446552da998320a43730dfdaadf5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65232528"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Настройка среды выполнения интеграции Azure-SSIS для высокой производительности

В этой статье описывается настройка среды выполнения интеграции Azure-SSIS для высокой производительности. Azure SSIS IR можно использовать для развертывания и запуска пакетов SQL Server Integration Services (SSIS) в Azure. Дополнительные сведения о среде выполнения интеграции Azure-SSIS IR см. в [этом разделе](concepts-integration-runtime.md#azure-ssis-integration-runtime). Сведения о развертывании и запуске пакетов SQL Server Integration Services (SSIS) в Azure см. в статье [Lift and shift SQL Server Integration Services workloads to the cloud](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview) (Перенос рабочих нагрузок SQL Server Integration Services в облако).

> [!IMPORTANT]
> Эта статья содержит результаты производительности и наблюдения за внутренним тестированием, выполняемым участниками группы разработки служб SSIS. Результаты могут различаться. Выполните тестирование до подготовки параметров конфигурации, которые могут повлиять на затраты и производительность.

## <a name="properties-to-configure"></a>Свойства, которые нужно настроить

В следующей части сценария конфигурации приведены свойства, которые можно настроить при создании среды выполнения интеграции Azure-SSIS. Полный сценарий и описание PowerShell см. в статье [Развертывание пакетов служб интеграции SQL Server (SSIS) в Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** — расположение для рабочего узла среды выполнения интеграции. Рабочий узел поддерживает постоянное подключение к базе данных каталога SSIS (SSISDB) в базе данных Azure SQL. Задайте для **AzureSSISLocation** то же расположение, что и для сервера Базы данных SQL, на котором размещена SSISDB, что позволяет среде выполнения интеграции работать максимально эффективно.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Служба "Фабрика данных Azure", включая среду выполнения интеграции MSSQL Integration Services Azure, поддерживает следующие параметры:
-   Standard\_A4\_v2
-   Standard\_A8\_v2;
-   Standard\_D1\_v2;
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2
-   Стандартный\_D2\_v3
-   Стандартный\_D4\_v3
-   Стандартный\_D8\_v3
-   Стандартный\_D16\_v3
-   Стандартный\_D32\_v3
-   Стандартный\_D64\_v3
-   Стандартный\_E2\_v3
-   Стандартный\_E4\_v3
-   Стандартный\_E8\_v3
-   Стандартный\_E16\_v3
-   Стандартный\_E32\_v3
-   Стандартный\_E64\_v3

В неофициальном внутреннем тестировании командой разработчиков служб SSIS оказалось, что устройства серии D (а не серии A) лучше использовать для выполнения пакета служб SSIS.

-   Соотношение производительности и цен серии D выше, чем серии A и соотношение производительности и цен серии v3 больше, чем серия dv2.
-   Пропускная способность для серий D выше, чем в сериях A такому же тарифу, и пропускную способность для серии v3 больше, чем серия dv2 по тому же тарифу.
-   Узлы серии версии 2 среды выполнения интеграции Azure SSIS не подходят для пользовательской установки, поэтому вместо этого используйте узлы серии v3. Если вы уже используете узлы серии v2, перейдите на узлы серии v3 как можно скорее.
-   E-series — размеры виртуальных Машин, которые предоставляет более высоким соотношением Ресурсов памяти, чем другие машины, оптимизированные для памяти. Если пакет требует большого объема памяти, можно, выбрав виртуальную Машину серии E.

### <a name="configure-for-execution-speed"></a>Настройка скорости выполнения
Если у вас немного пакетов, и вы хотите, чтобы они выполнялись быстро, используйте информацию на следующей схеме, чтобы выбрать тип виртуальной машины, подходящий для вашего сценария.

Далее предоставлены данные о выполнении одного пакета на одном рабочем узле. Пакет загружает 3 миллиона записей с именем первый и последний столбцы имен из хранилища BLOB-объектов Azure, создает столбец с полным именем и записи с полным именем длиннее 20 символов в хранилище BLOB-объектов Azure.

![Скорость выполнения пакета среды выполнения интеграции SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Настройка общей пропускной способности

Если у вас есть много пакетов, и для вас важна общая пропускная способность, используйте информацию на следующей схеме, чтобы выбрать тип виртуальной машины, подходящий для вашего сценария.

![Максимальная общая пропускная способность среды выполнения интеграции SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** корректирует масштабируемость среды выполнения интеграции. Пропускная способность среды выполнения интеграции пропорциональна **AzureSSISNodeNumber**. Сначала задайте для **AzureSSISNodeNumber** небольшое значение, контролируйте пропускную способность среды выполнения интеграции, а затем измените значение для своего сценария. Чтобы узнать, как изменить количество рабочих узлов, см. статью [Управление средой выполнения интеграции Azure SSIS](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Если вы уже используете мощный рабочий узел для запуска пакетов, увеличение значения **AzureSSISMaxParallelExecutionsPerNode** может повысить общую пропускную способность среды выполнения интеграции. Для узлов Standard_D1_v2 поддерживаются 1–4 параллельных выполнения на каждом узле. Для всех других типов узлов поддерживаются 1 max(2 x number of cores, 8) параллельных выполнений на каждом узле. Если вы хотите, чтобы **AzureSSISMaxParallelExecutionsPerNode** за пределы максимального значения, мы поддерживали, можно открыть запрос в службу поддержки, и мы увеличим значение max для вас и после, необходимо использовать Azure Powershell для обновления  **AzureSSISMaxParallelExecutionsPerNode**.
Вы можете определить подходящее значение на основе стоимости пакета и следующих конфигураций для рабочих узлов. Дополнительные сведения см. в статье [Размеры виртуальных машин общего назначения](../virtual-machines/windows/sizes-general.md).

| Размер             | Виртуальных ЦП | Память: ГиБ | Временное хранилище (SSD): ГиБ | Максимальная пропускная способность временного хранилища: операций ввода-вывода в секунду / операций чтения в Мбит/с / операций записи в Мбит/с | Максимальное число дисков данных / пропускная способность: IOPS | Максимальное число сетевых адаптеров и ожидаемая производительность сети (Мбит/с) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3.5         | 50                     | 3000 / 46 / 23                                             | 2 / 2x500                         | 2 / 750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4x500                         | 2 / 1500                                       |
| Standard\_D3\_v2 | 4\.    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8x500                         | 4 / 3000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16x500                       | 8 / 6000                                       |
| Standard\_A4\_v2 | 4\.    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8x500                         | 4 / 1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16x500                       | 8 / 2000                                       |
| Стандартный\_D2\_v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4 / 6 x 500                         | 2 / 1000                                       |
| Стандартный\_D4\_v3 | 4\.    | 16          | 100                    | 6000 / 93 / 46                                             | 8 / 12 x 500                        | 2 / 2000                                       |
| Стандартный\_D8\_v3 | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16 / 24 x 500                       | 4 / 4000                                       |
| Стандартный\_D16\_v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32 / 48 x 500                        | 8/8000                                       |
| Стандартный\_D32\_v3| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32 / 96 x 500                       | 8 / 16 000                                      |
| Стандартный\_D64\_v3| 64   | 256         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192 x 500                      | 8/30 000                                      |
| Стандартный\_E2\_v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4 / 6 x 500                         | 2 / 1000                                       |
| Стандартный\_E4\_v3 | 4\.    | 32          | 100                    | 6000 / 93 / 46                                             | 8 / 12 x 500                        | 2 / 2000                                       |
| Стандартный\_E8\_v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16 / 24 x 500                       | 4 / 4000                                       |
| Стандартный\_E16\_v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32 / 48 x 500                       | 8/8000                                       |
| Стандартный\_E32\_v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32 / 96 x 500                       | 8 / 16 000                                      |
| Стандартный\_E64\_v3| 64   | 432         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192 x 500                      | 8/30 000                                      |

Ниже приведены рекомендации по настройке правильного значения для свойства **AzureSSISMaxParallelExecutionsPerNode**. 

1. Сначала задайте для него небольшое значение.
2. Немного увеличьте его, чтобы проверить, улучшилась ли общая пропускная способность.
3. Перестаньте увеличивать значение, когда общая пропускная способность достигнет максимального значения.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

Ценовая категория **SSISDBPricingTier** предназначена для базы данных каталога SSIS (SSISDB) в базе данных Azure SQL. Этот параметр влияет на максимальное число рабочих ролей в экземпляре IR, на скорость помещения операции выполнения пакета в очередь, а также на скорость загрузки журнала выполнения.

-   Если для вас не имеет значения скорость помещения операции выполнения пакета в очередь и загрузки журнала выполнения, вы можете выбрать самый низкий уровень ценовой категории для базы данных. База данных SQL Azure с ценовой категорией "Базовый" поддерживает 8 рабочих ролей в экземпляре среды выполнения интеграции.

-   Выберите более эффективную базу данных, чем базовая, если вам требуется больше 8 рабочих ролей или больше 50 ядер. В противном случае база данных станет узким местом для экземпляра среды выполнения интеграции и отрицательно повлияет на общую производительность.

-   Выберите более эффективную базу данных, например, s3, если на подробный уровень ведения журнала. В соответствии с нашей неофициальном внутреннем тестировании, ценовой категории s3 может поддерживать выполнение пакета служб SSIS с 2 узлами, 128 параллельных счетчики и подробного ведения журнала.

Вы также можете настроить ценовую категорию базы данных на основе информации об использовании [единицы транзакций базы данных](../sql-database/sql-database-what-is-a-dtu.md) (DTU), доступной на портале Azure.

## <a name="design-for-high-performance"></a>Проектирование для обеспечения высокой производительности
Проектирование пакета SSIS для запуска в Azure отличается от проектирования пакета для локального выполнения. Для более эффективного выполнения в Azure SSIS IR вместо объединения нескольких независимых задач в том же пакете разделите их на несколько пакетов. Создайте выполнение для каждого пакета, чтобы не приходилось ожидать выполнения каждого из них. Такой подход обеспечивает масштабируемость среды выполнения интеграции Azure-SSIS и улучшает общую пропускную способность.

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше о среде выполнения интеграции Azure-SSIS в [этом разделе](concepts-integration-runtime.md#azure-ssis-integration-runtime).
