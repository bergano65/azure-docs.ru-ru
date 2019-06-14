---
title: Массовое копирование из базы данных с помощью элемента управления таблицы с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как с помощью шаблона решения Копировать массовых данных из базы данных с помощью внешнего элемента управления таблицы для хранения списка секции исходных таблиц с помощью фабрики данных Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.openlocfilehash: c4224693642e8c9f76deedc0c8ad8586e122cc23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60635451"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Массовое копирование из базы данных с помощью элемента управления таблицы

Чтобы скопировать данные из хранилища данных на сервере Oracle, Netezza, Teradata или SQL Server в хранилище данных SQL Azure, необходимо загрузить огромные объемы данных из нескольких таблиц. Как правило данные должен быть секционированы в каждой таблице, таким образом, можно загрузить строки с помощью нескольких потоков в параллельном режиме из одной таблицы. В этой статье описывается шаблон, используемый в этих сценариях.

 >! Обратите внимание, если вы хотите копировать данные из небольшое количество таблиц с относительно небольшим объемом данных тома в хранилище данных SQL, эффективнее использовать [средства копирования данных фабрики данных Azure](copy-data-tool.md). Шаблон, который описан в этой статье больше, чем необходимо для этого сценария.

## <a name="about-this-solution-template"></a>Информация о шаблоне решения

Этот шаблон извлекает список исходных секций базы данных для копирования из таблицы внешнего элемента управления. Затем он выполняет итерацию по каждой секции базы данных-источника и копирует данные в место назначения.

Шаблон состоит из трех действий.
- **Поиск** Получает список секций, что базы данных из таблицы внешнего элемента управления.
- **По каждому элементу** Получает список секции от действия поиска и выполняет итерацию каждой из секций для действия копирования.
- **Копировать** копирует каждой секции из исходного хранилища базы данных в целевое хранилище.

Шаблон определяет пять параметров.
- *Control_Table_Name* является таблицы внешнего элемента управления, который хранит список секционирования для базы данных-источника.
- *Control_Table_Schema_PartitionID* имя имя столбца в таблице внешнего элемента управления, хранится идентификатор каждой секции. Убедитесь, что идентификатор секции уникален для каждой секции базы данных-источника.
- *Control_Table_Schema_SourceTableName* вашего внешнего элемента управления таблица, в которой хранится имя каждой таблицы из базы данных-источника.
- *Control_Table_Schema_FilterQuery* имя столбца в таблице внешнего элемента управления, которые хранит фильтра запроса для получения данных из каждой секции базы данных-источника. Например, если данные секционированы по годам, запрос, который хранится в каждой строке может быть аналогичен "выберите * из источника данных где LastModifytime > = '' 2015-01-01 00:00:00 '' и LastModifytime < = «2015-12-31 23:59:59.999'' '.
- *Data_Destination_Folder_Path* — это путь, где данные копируются в хранилище данных назначения. Этот параметр отображается только в том случае, если целевой выбираемая файловое хранилище. Если хранилище данных SQL в качестве конечного хранилища, этот параметр не является обязательным. Но имена таблиц и схем в хранилище данных SQL должен быть таким же, как из базы данных-источника.

## <a name="how-to-use-this-solution-template"></a>Использование шаблона решения

1. Создайте таблицу элемента управления в SQL Server или базы данных SQL Azure для хранения исходного списка секции базы данных для массового копирования. В следующем примере существует пять секций базы данных-источника. Три секции, для *datasource_table*, а две используются для *таблицу project_table*. Столбец *LastModifytime* используется для секционирования данных в таблице *datasource_table* из базы данных-источника. Запрос, который используется для считывания первой секции — "выберите * из datasource_table где LastModifytime > = '' 2015-01-01 00:00:00 '' и LastModifytime < = «2015-12-31 23:59:59.999'' '. Можно использовать аналогичный запрос для чтения данных из других секций.

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

2. Перейдите к **массовое копирование из базы данных** шаблона. Создание **New** подключение к таблице внешний элемент управления, созданный на шаге 1.

    ![Создание подключения к контрольной таблице](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Создание **New** соединение, при копировании данных из базы данных-источника.

     ![Создание подключения к базе данных-источнику](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Создание **New** подключение к данным назначения хранения, что производится копирование данных.

    ![Создание подключения к целевому хранилищу](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Выберите **этот шаблон используется**.

    ![Использовать этот шаблон](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Отобразится конвейер, как показано в следующем примере:

    ![Проверка конвейера](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Выберите **Отладка**, введите **параметры**, а затем выберите **Готово**.

    ![Нажмите кнопку ** отладки **](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Отобразятся результаты, подобные приведенному ниже:

    ![Просмотр результатов](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Необязательно) Если выбрано хранилище данных SQL в качестве назначения данных, необходимо ввести подключения в хранилище BLOB-объектов Azure для промежуточного хранения, согласно требованиям Polybase хранилища данных SQL. Убедитесь, что контейнер в хранилище BLOB-объектов уже был создан.
    
    ![Настройка Polybase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о службе фабрики данных Azure, службе интеграции данных в облаке](introduction.md)
