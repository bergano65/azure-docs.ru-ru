---
title: Перенести хранение данных Azure из Gen1 в Gen2
description: Имитируйте хранилище озер данных Azure из Gen1 в Gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 80c0afafca3b0bf497689cbd4a0870eedd066cfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677143"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Перенести хранение данных Azure из Gen1 в Gen2

Вы можете перенести свои данные, рабочие нагрузки и приложения из Data Lake Storage Gen1 в Data Lake Storage Gen2.

Azure Data Lake Storage2 построен на [хранилище Azure Blob](storage-blobs-introduction.md) и предоставляет набор возможностей, посвященных аналитике больших данных. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) сочетает в себе функции [из Azure Data Lake Storage Gen1,](https://docs.microsoft.com/azure/data-lake-store/index)такие как семантика файловой системы, каталог и безопасность уровня файлов и масштаб с недорогим и многоуровневым хранилищем, возможностями высокой доступности/аварийного восстановления из [хранилища Azure Blob.](storage-blobs-introduction.md)

> [!NOTE]
> Для облегчения чтения в этой статье используется термин *Gen1* для обозначения данных Azure Data Lake Storage Gen1 и термин *Gen2* для обозначения хранилища azure Data Lake Data Gen2.

## <a name="recommended-approach"></a>Рекомендуемый подход

Чтобы перейти на Gen2, мы рекомендуем следующий подход.

:heavy_check_mark: Шаг 1: Оценка готовности

:heavy_check_mark: Шаг 2: Подготовка к миграции

:heavy_check_mark: Шаг 3: Миграция данных и рабочих нагрузок приложений

:heavy_check_mark: Шаг 4: ОтGen1 до Gen2

> [!NOTE]
> Gen1 и Gen2 - это разные службы, нет опыта обновления на месте, требуется преднамеренная миграция. 

### <a name="step-1-assess-readiness"></a>Шаг 1: Оценить готовность

1. Узнайте о [предложении Data Lake Storage Gen2;](https://azure.microsoft.com/services/storage/data-lake-storage/) это преимущества, затраты и общая архитектура. 

2. [Сравните возможности](#gen1-gen2-feature-comparison) Gen1 с возможностями Gen2. 

3. Просмотрите список [известных проблем](data-lake-storage-known-issues.md) для оценки пробелов в функциональности.

4. Gen2 поддерживает функции хранения данных Blob, такие как [диагностические журналы,](../common/storage-analytics-logging.md) [уровни доступа](storage-blob-storage-tiers.md)и [политики управления жизненным циклом хранения Blob.](storage-lifecycle-management-concepts.md) Если вы интересны в использовании любой из этих функций, просмотрите [текущий уровень поддержки.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-supported-blob-storage-features)

5. Просмотрите текущее состояние [поддержки экосистемы Azure,](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access) чтобы убедиться, что Gen2 поддерживает любые службы, от чего зависят ваши решения.

### <a name="step-2-prepare-to-migrate"></a>Шаг 2: Подготовка к миграции

1. Определите наборы данных, которые будут мигрировать.

   Воспользуйтесь этой возможностью, чтобы очистить наборы данных, которые вы больше не используете. Если вы не планируете мигрировать все данные одновременно, на этот раз, чтобы определить логические группы данных, которые можно мигрировать поэтапно.
   
2. Определите влияние, которое миграция окажет на ваш бизнес.

   Например, подумайте, можете ли вы позволить себе время простоя во время миграции. Эти соображения могут помочь вам определить подходящую схему миграции и выбрать наиболее подходящие инструменты.

3. Создайте план миграции. 

   Мы рекомендуем эти [модели миграции.](#migration-patterns) Вы можете выбрать один из этих шаблонов, объединить их вместе, или создать пользовательский шаблон самостоятельно.

### <a name="step-3-migrate-data-workloads-and-applications"></a>Шаг 3: Миграция данных, рабочих нагрузок и приложений

Мигрируйте данные, рабочие нагрузки и приложения, используя шаблон, который вы предпочитаете. Мы рекомендуем проверять сценарии поэтапно.

1. [Создайте учетную запись хранения](data-lake-storage-quickstart-create-account.md) и включите функцию иерархического пространства имен. 

2. Мигрируйте ваши данные. 

3. Направьте [службы в рабочие нагрузки,](data-lake-storage-integrate-with-azure-services.md) чтобы указать на конечную точку Gen2. 
   
4. Обновление приложений для использования AA Gen2. Смотрите руководства для [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [JavaScript](data-lake-storage-directory-file-acl-javascript.md) и [REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Обновление скриптов для использования data Lake Storage Gen2 [PowerShell cmdlets](data-lake-storage-directory-file-acl-powershell.md)и [команд Azure CLI.](data-lake-storage-directory-file-acl-cli.md)
   
6. Поиск ссылок URI, содержащих `adl://` строку в файлах кода, или в блокнотах Databricks, файлах Apache Hive H'L или любом другом файле, используемом как часть ваших рабочих нагрузок. Замените эти ссылки [на отформатированный Gen2 URI](data-lake-storage-introduction-abfs-uri.md) новой учетной записи хранилища. Например: Gen1 URI: `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`может стать . 

7. Настроят систему безопасности в учетной записи, включив [в нее роли на основе управления доступом (RBAC), безопасность](../common/storage-auth-aad-rbac-portal.md) [файлов и папок,](data-lake-storage-access-control.md)а также [брандмауэры Azure Storage и виртуальные сети.](../common/storage-network-security.md)

### <a name="step-4-cutover-from-gen1-to-gen2"></a>Шаг 4: Отgenальтов от Gen1 до Gen2

После того как вы уверены, что ваши приложения и рабочие нагрузки стабильны на Gen2, вы можете начать использовать Gen2 для удовлетворения ваших бизнес-сценариев. Выключите оставшиеся конвейеры, работающие на Gen1, и снимите с эксплуатации учетную запись Gen1. 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>Возможности Gen1 против Gen2

В этой таблице сравниваются возможности Gen1 с возможностями Gen2.

|Область |Поколение 1   |Поколение 2 |
|---|---|---|
|Упорядочение данных|[Иерархическое пространство имен](data-lake-storage-namespace.md)<br>Поддержка файлов и папок|[Иерархическое пространство имен](data-lake-storage-namespace.md)<br>Поддержка контейнеров, файлов и папок |
|Геоизбыточность| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ЗРС](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Аутентификация|[AAD управляемой идентичности](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Субъекты-службы](../../active-directory/develop/app-objects-and-service-principals.md)|[AAD управляемой идентичности](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Субъекты-службы](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Ключ общего доступа](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|Авторизация|Менеджмент - [RBAC](../../role-based-access-control/overview.md)<br>Данные - [ACLs](data-lake-storage-access-control.md)|Менеджмент - [RBAC](../../role-based-access-control/overview.md)<br>Данные - [ACLs](data-lake-storage-access-control.md), [RBAC](../../role-based-access-control/overview.md) |
|Шифрование - Данные в состоянии покоя|Сторона сервера - с [ключами, управляемыми корпорацией Майкрософт](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) или [управляемыми клиентами](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|Сторона сервера - с [ключами, управляемыми корпорацией Майкрософт](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) или [управляемыми клиентами](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Поддержка VNET|[Интеграция VNET](../../data-lake-store/data-lake-store-network-security.md)|[Конечные точки обслуживания](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Частные конечные точки](../common/storage-private-endpoints.md)|
|Взаимодействие с разработчиками|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|Общедоступные - [REST](/rest/api/storageservices/data-lake-storage-gen2), [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md)<br>Публичный предварительный просмотр - [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [PowerShell](data-lake-storage-directory-file-acl-powershell.md), [Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Журналы диагностики|Классические журналы<br>[Интегрированный Azure Monitor](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Классические журналы](../common/storage-analytics-logging.md) - Как правило, доступны<br>Интеграция мониторинга Azure - Хронология TBD|
|Экосистема|[HDInsight (3.6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3.1 и выше)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [S'L DW](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3.6, 4.0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5.1 и выше)](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [S'L DW](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>Модели Gen1 в Gen2

Выберите шаблон миграции, а затем измените этот шаблон по мере необходимости.

|||
|---|---|
|**Лифт и сдвиг**|Простейшая схема. Идеально, если ваши конвейеры данных могут позволить себе время простоя.|
|**Добавочное копирование**|Похож на *подъем и сдвиг,* но с меньшим временем простоя. Идеально подходит для больших объемов данных, копирование данных занимает больше времени.|
|**Двойной трубопровод**|Идеально подходит для трубопроводов, которые не могут позволить себе время простоя.|
|**Двунаправленная синхронизация**|Как и *двойной трубопровод,* но с более поэтапным подходом, который подходит для более сложных трубопроводов.|

Давайте подробнее рассмотрим каждый шаблон.
 
### <a name="lift-and-shift-pattern"></a>Шаблон подъема и смены

Это самый простой шаблон.

1. Остановить все пишет Gen1.

2. Перемещение данных с Gen1 в Gen2. Мы рекомендуем [фабрику данных Azure.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) ACL копируют данные.

3. Точка глотание операций и рабочих нагрузок для Gen2.

4. Вывод из эксплуатации Gen1.

> [!div class="mx-imgBorder"]
> ![подъемие и сдвиг шаблона](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Рассмотрение использования модели подъема и смены

:heavy_check_mark: Cutover от Gen1 до Gen2 для всех рабочих нагрузок одновременно.

:heavy_check_mark: Ожидать простоя во время миграции и периода сокращения.

:heavy_check_mark: Идеально подходит для конвейеров, которые могут позволить себе простои и все приложения могут быть обновлены одновременно.

### <a name="incremental-copy-pattern"></a>Инкрементный шаблон копирования

1. Начните перемещение данных с Gen1 в Gen2. Мы рекомендуем [фабрику данных Azure.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) ACL копируют данные.

2. Постепенно копируйте новые данные от Gen1.

3. После того, как все данные скопированы, прекратите все записи в Gen1 и направьте рабочие нагрузки на Gen2.

4. Вывод из эксплуатации Gen1.

> [!div class="mx-imgBorder"]
> ![Инкрементный шаблон копирования](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Рассмотрение вопроса об использовании шаблона дополнительных копий:

:heavy_check_mark: Cutover от Gen1 до Gen2 для всех рабочих нагрузок одновременно.

:heavy_check_mark: Ожидать простоя только в период сокращения.

:heavy_check_mark: Идеально подходит для конвейеров, где все приложения обновлялись одновременно, но копия данных требует больше времени.

### <a name="dual-pipeline-pattern"></a>Двойной шаблон трубопровода

1. Перемещение данных с Gen1 в Gen2. Мы рекомендуем [фабрику данных Azure.](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) ACL копируют данные.

2. Глотать новые данные как для Gen1, так и для Gen2.

3. Точка рабочих нагрузок для Gen2.

4. Остановить все пишет Gen1, а затем вывести из эксплуатации Gen1.

> [!div class="mx-imgBorder"]
> ![Двойной шаблон трубопровода](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Рассмотрение вопроса об использовании двойного шаблона трубопровода:

:heavy_check_mark: конвейеры Gen1 и Gen2 работают бок о бок.

:heavy_check_mark: Поддерживает нулевое время простоя.

:heavy_check_mark: Идеально подходит в ситуациях, когда ваши рабочие нагрузки и приложения не могут позволить себе простои, и вы можете глотать в обоих учетных записях хранения.

### <a name="bi-directional-sync-pattern"></a>Двухнаправленная синхронизация

1. Настройка двунаправленной репликации между Gen1 и Gen2. Мы рекомендуем [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/). Он предлагает функцию ремонта для существующих данных.

3. Когда все ходы будут завершены, прекратите все записи в Gen1 и выключите двунаправленную репликацию.

4. Вывод из эксплуатации Gen1.

> [!div class="mx-imgBorder"]
> ![Двунаправленный шаблон](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>Рассмотрение использования двухнаправленияйного шаблона синхронизации:

:heavy_check_mark: Идеально подходит для сложных сценариев, которые включают большое количество конвейеров и зависимостей, где поэтапный подход может иметь больше смысла.  

:heavy_check_mark: Миграционные усилия высоки, но они обеспечивают поддержку Gen1 и Gen2.

## <a name="next-steps"></a>Следующие шаги

- Узнайте о различных аспектах настройки безопасности для учетной записи хранилища. Смотрите [руководство по безопасности хранилища Azure](../common/storage-security-guide.md).
- Оптимизируйте производительность для вашего магазина data Lake Store. Просмотрите [оптимизацию хранения озер данных Azure Gen2 для производительности](data-lake-storage-performance-tuning-guidance.md)
- Просмотрите рекомендации по управлению магазином Data Lake Store. [Ознакомьтесь с рекомендациями по использованию Azure Data Lake Storage 2](data-lake-storage-best-practices.md)

