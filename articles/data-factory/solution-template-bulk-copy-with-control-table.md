---
title: Массовая копия из базы данных с помощью таблицы управления
description: Узнайте, как использовать шаблон решения для копирования объемных данных из базы данных с помощью таблицы внешнего управления для хранения списка исходных таблиц с помощью Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: 3a42d7da21cfb2e3066fbdd81b27c82155d8456f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75439927"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Массовая копия из базы данных с таблицей управления

Чтобы скопировать данные со склада данных в Oracle Server, Netezza, Teradata или s'L Server в хранилище данных Azure S'L, необходимо загрузить огромные объемы данных из нескольких таблиц. Обычно данные должны быть разделены в каждой таблице, так что вы можете загружать строки с несколькими потоками параллельно из одной таблицы. В этой статье описывается шаблон для использования в этих сценариях.

 >! ПРИМЕЧАНИЕ Если вы хотите скопировать данные из небольшого числа таблиц с относительно небольшим объемом данных в хранилище данных S'L, более эффективно использовать [инструмент копирования данных Azure Data Factory.](copy-data-tool.md) Шаблон, описанный в этой статье, больше, чем вам нужно для этого сценария.

## <a name="about-this-solution-template"></a>Информация о шаблоне решения

Этот шаблон получает список разделов исходной базы данных для копирования из таблицы внешнего управления. Затем он итерирует каждую раздел в исходной базе данных и копирует данные к месту назначения.

Шаблон состоит из трех действий.
- **Поиск** извлекает список верных разделов базы данных из таблицы внешнего управления.
- **ForEach** получает список разделов из действия Lookup и итерирует каждый раздел к действию Copy.
- **Копирование** каждой раздела из хранилища исходной базы данных в магазин назначения.

Шаблон определяет следующие параметры:
- *Control_Table_Name* — это ваша таблица внешнего управления, в которой хранится список разделов для исходной базы данных.
- *Control_Table_Schema_PartitionID* — это имя столбца в таблице внешнего управления, в которых хранится каждый идентификатор раздела. Убедитесь, что идентификатор раздела уникален для каждого раздела в исходной базе данных.
- *Control_Table_Schema_SourceTableName* — это ваша таблица внешнего управления, которая хранит каждое имя таблицы из исходной базы данных.
- *Control_Table_Schema_FilterQuery* — это имя столбца в таблице внешнего управления, в которых хранится запрос фильтра для получения данных из каждого раздела в исходной базе данных. Например, если вы разделите данные по годам, запрос, который хранится в каждой строке, может быть аналогичен "выбрать из источника данных, где LastModifytime >'2015-01-01 00:00:00'' и LastModifytime <'''2015-12-31 23:59:59.999'.
- *Data_Destination_Folder_Path* — это путь, по которому данные копируется в магазин назначения (применимо, когда местом назначения является "Файловая система" или "Azure Data Lake Storage Gen1"). 
- *Data_Destination_Container* — это путь корневой папки, на котором данные копируется в магазине назначения. 
- *Data_Destination_Directory* — это путь каталога под корнем, где данные копируется в магазин назначения. 

Последние три параметра, определяющие путь в магазине назначения, видны только в том случае, если местом назначения, который вы выбираете, является хранилище файлов. Если вы выберете в качестве хранилища «Azure Synapse Analytics» (ранее S'L DW)» в качестве хранилища назначения эти параметры не требуются. Но имена таблиц и схема в Хранилище данных S'L должны быть такими же, как и в исходной базе данных.

## <a name="how-to-use-this-solution-template"></a>Использование шаблона решения

1. Создайте таблицу управления в базе данных S'L Server или Azure S'L Для хранения списка разделов исходной базы данных для массовой копии. В следующем примере в исходной базе данных имеется пять разделов. Три перегородки для *datasource_table,* а два - для *project_table.* Столбец *LastModifytime* используется для раздела данных в таблице *datasource_table* из исходной базы данных. Запрос, который используется для чтения первого раздела, является "выбрать из datasource_table где LastModifytime >''2015-01-01 00:00:00'' и LastModifytime <''2015-12-31 23:59:59.999'.'. Аналогичный запрос можно использовать для чтения данных из других разделов.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Перейдите к **массовой копии из шаблона базы данных.** Создайте **новое** соединение с таблицей внешнего управления, созданной в шаге 1.

    ![Создание подключения к контрольной таблице](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Создайте **новое** соединение с исходной базой данных, из которого вы копируете данные.

    ![Создание подключения к базе данных-источнику](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Создайте **новое** соединение с хранилищем данных назначения, в которое вы копируете данные.

    ![Создание подключения к целевому хранилищу](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Выберите **Использовать этот шаблон**.

6. Вы видите конвейер, как показано в следующем примере:

    ![Проверка конвейера](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Выберите **отключать,** введите **параметры,** а затем выберите **Finish.**

    ![Нажмите кнопку «Дебуг»](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Вы видите результаты, аналогичные следующему примеру:

    ![Просмотр результатов](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Необязательно) Если вы выбрали в качестве пункта назначения данных «Azure Synapse Analytics» (ранее S'L DW)» (место назначения данных), необходимо ввести подключение к хранилищу Azure Blob для постановки, как того требует Polybase хранилища данных. Шаблон автоматически генерирует траекторию контейнера для хранения Blob. Проверьте, создан ли контейнер после запуска конвейера.
    
    ![Настройка Polybase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Дальнейшие действия

- [Знакомство с Фабрикой данных Azure](introduction.md)
