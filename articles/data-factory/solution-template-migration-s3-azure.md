---
title: Перенос данных с Amazon S3 в Azure Data Lake Storage Gen2
description: Узнайте, как использовать шаблон решения для переноса данных из Amazon S3, используя таблицу внешнего управления для хранения списка разделов на AWS S3 с Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: 23d799f84cb3ac3ca911a5669041b0a25394a7ff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414761"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Перенос данных с Amazon S3 в Azure Data Lake Storage Gen2

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Используйте шаблоны для переноса петабайт данных, состоящих из сотен миллионов файлов от Amazon S3 до Azure Data Lake Storage Gen2. 

 > [!NOTE]
 > Если вы хотите скопировать небольшой объем данных с AWS S3 в Azure (например, менее 10 ТБ), это более эффективно и просто использовать [инструмент копирования данных Azure Data Factory.](copy-data-tool.md) Шаблон, описанный в этой статье, больше, чем то, что вам нужно.

## <a name="about-the-solution-templates"></a>О шаблонах решений

Раздел данных рекомендуется особенно при миграции более 10 ТБ данных. Чтобы развести данные, используйте настройку "префикс" для фильтрации папок и файлов на Amazon S3 по имени, а затем каждое задание копии ADF может копировать одну перегородку за один раз. Вы можете запустить несколько заданий копирования ADF одновременно для лучшей пропускной всей входной.

Миграция данных обычно требует одноразовой исторической миграции данных плюс периодическая синхронизация изменений от AWS S3 до Azure. Ниже приведены два шаблона, где один шаблон охватывает одноразовую историческую миграцию данных, а другой — синхронизацию изменений от AWS S3 до Azure.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Для того, чтобы шаблон мигрировал исторические данные с Amazon S3 в Azure Data Lake Storage Gen2

Этот шаблон *(имя шаблона: перенос исторических данных из AWS S3 в Azure Data Lake Storage Gen2)* предполагает, что вы написали список разделов в таблице внешнего управления в базе данных Azure S'L. Таким образом, он будет использовать действие *Lookup* для получения списка разделов из таблицы внешнего управления, итерации по каждому разделу и сделать каждую копию вакансии ADF по одному разделу за один раз. После завершения выполнения любой задания копирования используется действие *Stored Procedure* для обновления статуса копирования каждого раздела в таблице управления.

Шаблон содержит пять действий:
- **Lookup** извлекает разделы, которые не были скопированы в Azure Data Lake Storage Gen2, из внешней таблицы управления. Название таблицы *s3_partition_control_table,* а запрос на загрузку данных из таблицы : *"SELECT PartitionPrefix from s3_partition_control_table WHERE SuccessOrFailure - 0".*
- **ForEach** получает список разделов из действия *Lookup* и итерирует каждый раздел в действие *TriggerCopy.* Можно настроить *batchCount* для одновременного выполнения нескольких заданий копирования ADF. Мы установили 2 в этом шаблоне.
- **ExecutePipeline** выполняет конвейер *CopyFolderPartitionFromS3.* Причина, по которой мы создаем еще один конвейер, чтобы сделать каждую копию-копию раздела, заключается в том, что это позволит легко перезапустить неудавшую работу копирования для перезагрузки этого конкретного раздела из AWS S3. Все остальные задания копирования, загружаемые другими разделами, не будут затронуты.
- **Копирование** каждой раздела от AWS S3 до Azure Data Lake Storage Gen2.
- **SqlServerStoredProcedure** обновляет статус копирования каждой раздела в таблице управления.

Шаблон содержит два параметра:
- **AWS_S3_bucketName** это ваше имя ведра на AWS S3, где вы хотите перенести данные из. Если требуется перенести данные из нескольких ведер на AWS S3, можно добавить еще один столбец в таблице внешнего управления для хранения имени ведра для каждого раздела, а также обновить конвейер для получения данных из этого столбца соответственно.
- **Azure_Storage_fileSystem** это имя файлаSystem на Azure Data Lake Storage 2, куда требуется перенести данные.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Для того, чтобы шаблон скопировал измененные файлы только с Amazon S3 в Azure Data Lake Storage Gen2

Этот шаблон *(имя шаблона: копировать данные дельты с AWS S3 в Azure Data Lake Storage Gen2*) использует LastModifiedTime каждого файла для копирования новых или обновленных файлов только от AWS S3 до Azure. Имейте в виду, если ваши файлы или папки уже были разделены временем с timeslice информации как часть файла или папки имя на AWS S3 (например, / yyyy/mm/dd/file.csv), вы можете перейти к этому [учебнику,](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) чтобы получить более эффективный подход для дополнительной загрузки новых файлов. Этот шаблон предполагает, что вы написали список разделов в таблице внешнего управления в базе данных Azure S'L. Таким образом, он будет использовать действие *Lookup* для получения списка разделов из таблицы внешнего управления, итерации по каждому разделу и сделать каждую копию вакансии ADF по одному разделу за один раз. Когда каждое задание копии начинает копировать файлы из AWS S3, оно полагается на свойство LastModifiedTime для идентификации и копирования только новых или обновленных файлов. После завершения выполнения любой задания копирования используется действие *Stored Procedure* для обновления статуса копирования каждого раздела в таблице управления.

Шаблон содержит семь действий:
- **Поиск** извлекает разделы из таблицы внешнего управления. Название таблицы *s3_partition_delta_control_table,* а запрос для загрузки данных из таблицы *"выбрать различные разделыPrefix из s3_partition_delta_control_table"*.
- **ForEach** получает список разделов из действия *Lookup* и итерирует каждый раздел в действие *TriggerDeltaCopy.* Можно настроить *batchCount* для одновременного выполнения нескольких заданий копирования ADF. Мы установили 2 в этом шаблоне.
- **ExecutePipeline** выполняет конвейер *DeltaCopyFolderPartitionFromS3.* Причина, по которой мы создаем еще один конвейер, чтобы сделать каждую копию-копию раздела, заключается в том, что это позволит легко перезапустить неудавшую работу копирования для перезагрузки этого конкретного раздела из AWS S3. Все остальные задания копирования, загружаемые другими разделами, не будут затронуты.
- **Поиск** извлекает последнее время выполнения задания копий из таблицы внешнего управления, чтобы новые или обновленные файлы можно было идентифицировать через LastModifiedTime. Название таблицы *s3_partition_delta_control_table,* а запрос для загрузки данных из таблицы — *«выберите max (JobRunTime) как LastModifiedTime из s3_partition_delta_control_table где разделPrefix » »'pipeline().parameters.prefixStr» и SuccessOrFailure 1»*.
- **Копирование** новых или измененных файлов только для каждого раздела от AWS S3 до Azure Data Lake Storage Gen2. Свойство *модифицированногоDatetimeStart* устанавливается до последнего времени выполнения работы копии. Свойство *модифицированногоDatetimeEnd* устанавливается на текущее время выполнения задания копирования. Имейте в виду, что время применяется к часовому поясу UTC.
- **SqlServerStoredProcedure** обновляет статус копирования каждого раздела и копирования времени выполнения в таблице управления, когда это удается. Колонка SuccessOrFailure установлена на 1.
- **SqlServerStoredProcedure** обновляет статус копирования каждого раздела и копирования времени выполнения в таблице управления, когда он не удается. Колонка SuccessOrFailure установлена на 0.

Шаблон содержит два параметра:
- **AWS_S3_bucketName** это ваше имя ведра на AWS S3, где вы хотите перенести данные из. Если требуется перенести данные из нескольких ведер на AWS S3, можно добавить еще один столбец в таблице внешнего управления для хранения имени ведра для каждого раздела, а также обновить конвейер для получения данных из этого столбца соответственно.
- **Azure_Storage_fileSystem** это имя файлаSystem на Azure Data Lake Storage 2, куда требуется перенести данные.

## <a name="how-to-use-these-two-solution-templates"></a>Как использовать эти два шаблона решения

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Для того, чтобы шаблон мигрировал исторические данные с Amazon S3 в Azure Data Lake Storage Gen2 

1. Создайте таблицу управления в базе данных Azure S'L для хранения списка разделов AWS S3. 

    > [!NOTE]
    > Название таблицы s3_partition_control_table.
    > Схема таблицы управления — PartitionPrefix и SuccessOrFailure, где РазделPrefix является настройкой префикса в S3 для фильтрации папок и файлов в Amazon S3 по имени, а SuccessOrFailure — это статус копирования каждой раздела: 0 означает, что эта разделнена не была скопирована на Azure и 1 означает, что эта часть была успешно скопирована в Azure.
    > В таблице управления определено 5 разделов, а состояние по умолчанию копирования каждого раздела составляет 0.

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. Создайте процедуру хранения на той же базе данных Azure S'L для контрольной таблицы. 

    > [!NOTE]
    > Название Процедуры хранения sp_update_partition_success. Он будет вызываться активностью SqlServerStoredProcedure в вашем конвейере ADF.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. Перейдите к историческим данным Мигрировать с шаблона **AWS S3 в шаблон Azure Data Lake Storage Gen2.** Ввейте соединения в таблицу внешнего управления, AWS S3 в качестве хранилища исходных данных и Хранилище azure Data Lake Storage Gen2 в качестве хранилища назначения. Имейте в виду, что таблица внешнего управления и сохраненная процедура являются ссылкой на одно и то же соединение.

    ![Создание подключения](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Выберите **Использовать этот шаблон**.

    ![Использовать этот шаблон](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. Вы видите 2 конвейера и 3 набора данных были созданы, как показано в следующем примере:

    ![Проверка конвейера](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Выберите **отключать,** введите **параметры,** а затем выберите **Finish.**

    ![Нажмите кнопку «Дебуг»](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. Вы видите результаты, аналогичные следующему примеру:

    ![Просмотр результатов](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Для того, чтобы шаблон скопировал измененные файлы только с Amazon S3 в Azure Data Lake Storage Gen2

1. Создайте таблицу управления в базе данных Azure S'L для хранения списка разделов AWS S3. 

    > [!NOTE]
    > Название таблицы s3_partition_delta_control_table.
    > Схема таблицы управления - разделPrefix, JobRunTime и SuccessOrFailure, где разделPrefix является настройкой префикса в S3 для фильтрации папок и файлов в Amazon S3 по имени, JobRunTime — это значение времени даты при запуске копирования заданий, а SuccessOrFailure — это статус копирования каждой раздела: 0 означает, что эта перегородка не была скопирована в Azure и 1 означает, что эта перегородка была успешно скопирована.
    > В таблице управления определено 5 разделов. Значение по умолчанию для JobRunTime может быть временем, когда начинается одноразовая историческая миграция данных. Активность копирования ADF будет копировать файлы на AWS S3, которые в последний раз были изменены после этого времени. Состояние по умолчанию копирования каждого раздела составляет 1.

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. Создайте процедуру хранения на той же базе данных Azure S'L для контрольной таблицы. 

    > [!NOTE]
    > Название Процедуры хранения sp_insert_partition_JobRunTime_success. Он будет вызываться активностью SqlServerStoredProcedure в вашем конвейере ADF.

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. Перейдите к данным Copy delta с шаблона **AWS S3 в Azure Data Lake Storage Gen2.** Ввейте соединения в таблицу внешнего управления, AWS S3 в качестве хранилища исходных данных и Хранилище azure Data Lake Storage Gen2 в качестве хранилища назначения. Имейте в виду, что таблица внешнего управления и сохраненная процедура являются ссылкой на одно и то же соединение.

    ![Создание подключения](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Выберите **Использовать этот шаблон**.

    ![Использовать этот шаблон](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. Вы видите 2 конвейера и 3 набора данных были созданы, как показано в следующем примере:

    ![Проверка конвейера](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6. Выберите **отключать,** введите **параметры,** а затем выберите **Finish.**

    ![Нажмите кнопку «Дебуг»](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. Вы видите результаты, аналогичные следующему примеру:

    ![Просмотр результатов](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. Вы также можете проверить результаты из таблицы управления по запросу *"выбрать из s3_partition_delta_control_table"*, вы увидите выход, похожий на следующий пример:

    ![Просмотр результатов](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Дальнейшие действия

- [Копирование файлов из нескольких контейнеров](solution-template-copy-files-multiple-containers.md)
- [Перемещение файлов](solution-template-move-files.md)