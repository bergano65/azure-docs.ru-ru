---
title: SQL Server SQL Server на виртуальной машине Azure (Обзор миграции)
description: Узнайте о различных стратегиях миграции, когда вы хотите перенести SQL Server в SQL Server на виртуальных машинах Azure.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: ''
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: d47abaade13958b4e28d3ad5f62b88e8a53e89a9
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917847"
---
# <a name="migration-overview-sql-server-to-sql-server-on-azure-vms"></a>Обзор миграции: SQL Server для SQL Server на виртуальных машинах Azure
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Узнайте о различных стратегиях миграции, которые помогут перенести SQL Server SQL Server на виртуальные машины Azure. 

Вы можете выполнить миграцию SQL Server, выполняемого локально или в:

- SQL Server на виртуальных машинах  
- Amazon Web Services (AWS) EC2 
- Служба реляционной базы данных Amazon (AWS RDS) 
- Подсистема вычислений (Google Cloud Platform-обеспечить)

Другие сценарии см. в разделе [руководств по миграции баз данных](https://datamigration.microsoft.com/). 

## <a name="overview"></a>Обзор

Переход на [SQL Server на виртуальных машинах Azure](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview) , если вы хотите использовать знакомую среду SQL Server с ЭЛЕМЕНТОМ управления ОС и хотите воспользоваться преимуществами предоставляемых облаком функций, таких как высокая доступность виртуальных машин, [Автоматическая архивация](../../virtual-machines/windows/automated-backup.md)и [Автоматическая установка исправлений](../../virtual-machines/windows/automated-patching.md). 

Вы можете сэкономить на затратах, применяя собственную лицензию [преимущество гибридного использования Azure модели лицензирования](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md) или расширяя поддержку SQL Server 2008 и SQL Server 2008 R2, получая [Бесплатные обновления для системы безопасности](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md). 


## <a name="choosing-appropriate-target"></a>Выбор подходящего целевого объекта

Виртуальные машины Azure работают в различных регионах Azure, а также предлагают различные [размеры компьютеров](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) и [возможности хранения](https://docs.microsoft.com/azure/virtual-machines/disks-types). При определении правильного размера виртуальной машины и хранилища для рабочей нагрузки SQL Server см [. рекомендации по производительности SQL Server на виртуальных машинах Azure.](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices#vm-size-guidance) Чтобы определить размер виртуальной машины и требования к хранению для рабочей нагрузки. рекомендуется изменять их размер с помощью Performance-Based оценки службы " [Миграция Azure](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#types-of-assessments)". Если этот параметр недоступен, ознакомьтесь со следующей статьей о создании собственного [базового плана для повышения производительности](https://azure.microsoft.com/services/virtual-machines/sql-server/).

Обратите внимание на правильную установку и настройку SQL Server на виртуальной машине. Рекомендуется использовать [коллекцию образов виртуальных машин SQL Azure](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/create-sql-vm-portal) , так как это позволяет создать SQL SERVERную виртуальную машину с правильной версией, выпуском и операционной системой. Это также позволит автоматически зарегистрировать виртуальную машину Azure с [поставщиком ресурсов](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/create-sql-vm-portal) SQL Server, включив такие функции, как автоматическое резервное копирование и автоматическая установка исправлений.

## <a name="migration-strategies"></a>Стратегии миграции

Существует две стратегии миграции пользовательских баз данных в экземпляр SQL Server на виртуальных машинах Azure: **миграция, перенос** **и сдвиг**. 

Подход, подходящий для вашего бизнеса, обычно зависит от следующих факторов: 

- Размер и масштаб миграции
- Скорость миграции
- Поддержка приложений для изменения кода
- Необходимо изменить SQL Server версию, операционную систему или и то, и другое.
- Жизненный цикл поддержки существующих продуктов
- Окно для простоя приложения во время миграции

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-individual-databases-guide/virtual-machine-migration-downtime.png" alt-text="простой миграции виртуальной машины":::

В следующей таблице описаны различия в двух стратегиях миграции.
<br />

| **Стратегия миграции** | **Описание** | **Сценарии использования** |
| --- | --- | --- |
| **Сдвиг &** | Используйте стратегию миграции точности и сдвига, чтобы переместить все физические или виртуальные SQL Server из текущего расположения в экземпляр SQL Server на виртуальной машине Azure без каких либо изменений в операционной системе, либо SQL Server версии. Чтобы выполнить миграцию с прогнозированием и сдвигом, см. статью [Миграция в Azure](../../../migrate/migrate-services-overview.md). <br /><br /> Исходный сервер остается в оперативном режиме и выполняет запросы служб, пока исходный и целевой сервер синхронизируют данные, что позволяет почти без проблем выполнить миграцию. | Используйте для одномасштабных миграций, даже для таких сценариев, как выход из центра обработки данных. <br /><br /> Минимальное отсутствие изменений кода, необходимых для пользовательских баз данных SQL или приложений, что позволяет быстрее выполнять общие операции миграции. <br /><br />Для миграции служб бизнес-аналитики, таких как  [SSIS](/sql/integration-services/sql-server-integration-services), [SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports)и [SSAS](/analysis-services/analysis-services-overview), не требуется никаких дополнительных действий. |
|**анализа** | Используйте стратегию миграции, если требуется обновить целевую SQL Server и (или) версию операционной системы. <br /> <br /> Выберите виртуальную машину Azure из Azure Marketplace или подготовленный образ SQL Server, соответствующий исходной версии SQL Server. | Используйте, если существует требование или требуется использовать функции, доступные в более новых версиях SQL Server, или если требуется обновить устаревшие SQL Server и (или) версии ОС, которые больше не поддерживаются.  <br /> <br /> Для поддержки обновления SQL Server может потребоваться внести изменения в базу данных приложения или пользователя. <br /><br />При переносе служб [бизнес-аналитики](#business-intelligence) в области миграции могут возникнуть дополнительные соображения. |


## <a name="lift-and-shift"></a>Методика lift-and-shift  

В следующей таблице подробно описан способ переноса базы данных SQL Server в SQL Server на виртуальных машинах Azure с помощью стратегии миграции с **прогнозированием и сдвигом** .
<br />

|**Метод** | **Минимальная версия источника** | **Минимальная Целевая версия** | **Ограничение на размер исходного резервного копирования** |  **Примечания** |
| --- | --- | --- | --- | --- |
| [Миграция Azure](../../../migrate/index.yml) | SQL Server 2008 SP4| SQL Server 2008 SP4| [Ограничение на размер хранилища виртуальной машины Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  Существующие SQL Server будут перемещены "как есть" в экземпляр SQL Server на виртуальной машине Azure. Позволяет масштабировать рабочие нагрузки миграции до 35 000 виртуальных машин. <br /><br /> Исходные серверы остаются в сети и обслуживают запросы во время синхронизации данных сервера, минимизируя время простоя. <br /><br /> **Автоматизация & скриптов**: [Azure Site Recovery сценарии](../../../migrate/how-to-migrate-at-scale.md) и [Пример масштабируемой миграции и планирования для Azure](/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)|

## <a name="migrate"></a>Миграция  

Из-за простоты установки рекомендуемый подход к миграции заключается в том, чтобы локально создать [резервную копию](/sql/t-sql/statements/backup-transact-sql) собственного SQL Server, а затем скопировать файл в Azure. Этот метод поддерживает большие базы данных (>1 ТБ) для всех версий SQL Server, начиная с 2008 и более крупных резервных копий базы данных (>1 ТБ). Однако для баз данных, начиная с SQL Server 2014, размер которых меньше 1 ТБ и имеют хорошее подключение к Azure, [SQL Server резервное копирование по URL-адресу](/sql/relational-databases/backup-restore/sql-server-backup-to-url) является лучшим подходом. 

При переносе баз данных SQL Server в экземпляр SQL Server на виртуальных машинах Azure важно выбрать подход, который подходит, когда необходимо прямую миграцию на целевой сервер, так как это влияет на окно "время простоя приложения".

В следующей таблице приведены все доступные методы переноса базы данных SQL Server в SQL Server на виртуальных машинах Azure.
<br />

|**Метод** | **Минимальная версия источника** | **Минимальная Целевая версия** | **Ограничение на размер исходного резервного копирования** | **Примечания** |
| --- | --- | --- | --- | --- |
| **[Резервное копирование в файл](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 | SQL Server 2008 SP4| [Ограничение на размер хранилища виртуальной машины Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  Это простой и хорошо проверенный способ перемещения баз данных между компьютерами. Используйте сжатие, чтобы максимально сжать резервный размер для перемещения. <br /><br /> **Автоматизация & скриптов**: [Transact-SQL (T-SQL)](/sql/t-sql/statements/backup-transact-sql) и [AzCopy в хранилище BLOB-объектов](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[Резервное копирование на URL-адрес](/sql/relational-databases/backup-restore/sql-server-backup-to-url)** | SQL Server 2012 SP1 CU2 | SQL Server 2012 SP1 CU2| 12,8 ТБ для SQL Server 2016; в противном случае 1 ТБ | Альтернативный способ перемещения файла резервной копии на виртуальную машину с помощью службы хранилища Azure. Используйте сжатие, чтобы максимально сжать резервный размер для перемещения. <br /><br /> **Автоматизация & скриптов**:  [T-SQL или план обслуживания](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[Помощник по переносу баз данных (DMA)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 SP4| [Ограничение на размер хранилища виртуальной машины Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  [DMA](/sql/dma/dma-overview) оценивает SQL Server локально, а затем без проблем обновляет до более поздних версий SQL Server или переносит на SQL Server на виртуальных машинах Azure, в базе данных SQL azure или управляемый экземпляр SQL Azure. <br /><br /> Не следует использовать в пользовательских базах данных с поддержкой FILESTREAM.<br /><br /> DMA также включает возможность переноса [имен входа SQL и Windows](/sql/dma/dma-migrateserverlogins) и оценки [пакетов служб SSIS](/sql/dma/dma-assess-ssis). <br /><br /> **Автоматизация & скриптов**: [интерфейс командной строки](/sql/dma/dma-commandline) |
| **[Отсоединение и присоединение](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 SP4 | SQL Server 2014 | [Ограничение на размер хранилища виртуальной машины Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Используйте этот метод, если вы планируете [хранить эти файлы с помощью службы хранилища BLOB-объектов Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) и подключаете их к экземпляру SQL Server на виртуальной машине Azure, особенно полезном при работе с очень большими базами данных или когда время резервного копирования и восстановления слишком велико. <br /><br /> **Автоматизация & скриптов**:  [T-SQL](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) и [AzCopy в хранилище BLOB-объектов](../../../storage/common/storage-use-azcopy-v10.md)|
|**[доставка журналов;](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 (только для Windows) | SQL Server 2008 SP4 (только для Windows) | [Ограничение на размер хранилища виртуальной машины Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Доставка журналов реплицирует файлы журналов транзакций из локальной среды в экземпляр SQL Server на виртуальной машине Azure. <br /><br /> Это обеспечивает минимальный простой во время отработки отказа и требует меньше затрат на настройку, чем настройка группы доступности Always On. <br /><br /> **Автоматизация & скриптов**: [T-SQL](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[Распределенная группа доступности](../../virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Ограничение на размер хранилища виртуальной машины Azure](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |  [Распределенная группа доступности](/sql/database-engine/availability-groups/windows/distributed-availability-groups) — это особый тип группы доступности, охватывающий две отдельные группы доступности. Группы доступности, участвующие в распределенной группе доступности, не должны находиться в одном расположении и включать междоменную поддержку. <br /><br /> Этот метод сокращает время простоя, используя, когда группа доступности настроена в локальной среде. <br /><br /> **Автоматизация & скриптов**: [T-SQL](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;
 
> [!TIP]
> Сведения о передаче больших данных с ограничением "нет сети" см. в статье [Передача больших данных с ограниченным подключением](../../../storage/common/storage-solution-large-dataset-low-network.md).
> 

### <a name="considerations"></a>Рекомендации

Ниже приведен список ключевых моментов, которые следует учитывать при проверке методов миграции.

- Для оптимальной производительности при переносе данных перенесите базы данных и файлы на экземпляр SQL Server на виртуальной машине Azure с помощью сжатого файла резервной копии. Для больших баз данных, помимо сжатия, [Разделите файл резервной копии на файлы меньшего размера](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server) , чтобы повысить производительность во время резервного копирования и перемещения. 
- При переходе с SQL Server 2014 или более поздней версии рекомендуется [зашифровать резервные копии](/sql/relational-databases/backup-restore/backup-encryption) для защиты данных во время сетевой пересылки.
- Чтобы сократить время простоя при переносе базы данных, используйте параметр группы доступности Always On. 
- Чтобы сократить время простоя без дополнительных затрат на настройку группы доступности, используйте параметр доставки журналов. 
- Для параметра ограничено отсутствием сети используйте методы автономной миграции, такие как резервное копирование и восстановление, или [службы обмена дисками](../../../storage/common/storage-solution-large-dataset-low-network.md) , доступные в Azure.
- Чтобы также изменить версию SQL Server на SQL Server на виртуальной машине Azure, см. раздел [change SQL Server Edition](../../virtual-machines/windows/change-sql-server-edition.md).

## <a name="business-intelligence"></a>Бизнес-аналитика 

При миграции SQL Server служб бизнес-аналитики вне области миграции пользовательской базы данных могут возникнуть дополнительные соображения. 

К этим службам относятся следующие:

- [**SQL Server Integration Services (службы SSIS)**](/sql/integration-services/install-windows/upgrade-integration-services)
- [**SQL Server Reporting Services (службы SSRS)**](/sql/reporting-services/install-windows/upgrade-and-migrate-reporting-services)
- [**SQL Server Analysis Services (SSAS)**](/sql/database-engine/install-windows/upgrade-analysis-services)

## <a name="supported-versions"></a>Поддерживаемые версии

При подготовке к переносу баз данных SQL Server в SQL Server на виртуальных машинах Azure следует учитывать поддерживаемые версии SQL Server. Список текущих поддерживаемых версий SQL Server на виртуальных машинах Azure см. на [SQL Server на виртуальных машинах Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms).


## <a name="next-steps"></a>Следующие шаги

Чтобы приступить к переносу баз данных SQL Server в SQL Server на виртуальных машинах Azure, ознакомьтесь с [руководством по миграции отдельных баз данных](sql-server-to-sql-on-azure-vm-individual-databases-guide.md). 

- Сведения о службах и средствах Майкрософт и сторонних поставщиков, которые помогут вам в использовании различных сценариев переноса баз данных и данных, а также специальных задач см. в статье [служба и средства для переноса данных.](../../../dms/dms-tools-matrix.md)

- Дополнительные сведения о SQL Azure см. в следующих статьях:
   - [Варианты развертывания](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server на виртуальных машинах Azure](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Калькулятор совокупной стоимости владения Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Дополнительные сведения о структуре и цикле внедрения для миграции в облако см. в разделе
   -  [Cloud Adoption Framework для Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Рекомендации по затратам и изменению размеров рабочих нагрузок, переносятся в Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Сведения о лицензировании см. в разделе
   - [Использование собственной лицензии с Преимущество гибридного использования Azure](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Бесплатная расширенная поддержка для SQL Server 2008 и SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Чтобы оценить уровень доступа к приложениям, см. раздел [набор средств миграции для доступа к данным (Предварительная версия)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Дополнительные сведения о выполнении проверки уровня доступа к данным A/B см. [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
