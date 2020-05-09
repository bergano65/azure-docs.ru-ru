---
title: Групповое копирование из базы данных с помощью управляющей таблицы
description: Узнайте, как использовать шаблон решения для копирования данных большого объема из базы данных с помощью внешней таблицы управления для хранения списка секций исходных таблиц с помощью фабрики данных Azure.
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
ms.openlocfilehash: 46e81242c1fba463f547015a244650ae6e574580
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629088"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Групповое копирование из базы данных с помощью таблицы элементов управления

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Чтобы скопировать данные из хранилища данных на сервере Oracle, Netezza, Teradata или SQL Server в хранилище данных SQL Azure, необходимо загрузить огромные объемы данных из нескольких таблиц. Обычно данные должны быть секционированы в каждой таблице, чтобы можно было параллельно загружать строки с несколькими потоками из одной таблицы. В этой статье описывается шаблон для использования в этих сценариях.

 >! Примечание. Если вы хотите скопировать данные из небольшого числа таблиц с относительно небольшим объемом данных в хранилище данных SQL, более эффективно использовать [средство копирование данных фабрики данных Azure](copy-data-tool.md). Шаблон, описанный в этой статье, больше, чем требуется для этого сценария.

## <a name="about-this-solution-template"></a>Информация о шаблоне решения

Этот шаблон получает список секций базы данных-источника для копирования из внешней таблицы управления. Затем он выполняет итерацию по каждой секции в базе данных источника и копирует данные в место назначения.

Шаблон состоит из трех действий.
- **Уточняющий запрос** извлекает список всех секций базы данных из внешней таблицы управления.
- **Foreach** получает список секций из действия уточняющего запроса и выполняет итерацию каждой секции с действием копирования.
- **Copy** копирует каждую секцию из исходного хранилища базы данных в целевое хранилище.

Шаблон определяет следующие параметры:
- *Control_Table_Name* — это внешняя таблица управления, в которой хранится список секций для базы данных-источника.
- *Control_Table_Schema_PartitionID* — это имя столбца в таблице внешнего элемента управления, в которой хранится идентификатор каждого раздела. Убедитесь, что идентификатор секции уникален для каждой секции в базе данных-источнике.
- *Control_Table_Schema_SourceTableName* — это внешняя таблица управления, в которой хранится имя каждой таблицы из базы данных-источника.
- *Control_Table_Schema_FilterQuery* — имя столбца в таблице внешнего элемента управления, в котором хранится запрос фильтра для получения данных из каждой секции в базе данных-источнике. Например, если данные секционированы по годам, то запрос, хранящийся в каждой строке, может быть похож на "SELECT * FROM DataSource WHERE LastModifytime >= ' ' 2015-01-01 00:00:00 ' ' and LastModifytime <= ' ' 2015-12-31 23:59:59.999 ' ' '.
- *Data_Destination_Folder_Path* — это путь, по которому данные копируются в целевое хранилище (применимо, если выбрано значение "файловая система" или "Azure Data Lake Storage 1-го поколения"). 
- *Data_Destination_Container* — это путь к корневой папке, куда копируются данные в целевом хранилище. 
- *Data_Destination_Directory* — это путь к каталогу в корневом каталоге, куда данные копируются в целевое хранилище. 

Последние три параметра, определяющие путь в целевом хранилище, отображаются только в том случае, если выбранное назначение является хранилищем на основе файлов. Если в качестве целевого хранилища выбрано "Azure синапсе Analytics (ранее SQL DW)", эти параметры не требуются. Однако имена таблиц и схема в хранилище данных SQL должны совпадать с именами в базе данных источника.

## <a name="how-to-use-this-solution-template"></a>Использование шаблона решения

1. Создайте таблицу элементов управления в SQL Server или базе данных SQL Azure, чтобы сохранить список разделов исходной базы данных для операции копирования. В следующем примере имеется пять секций в базе данных источника. Три секции предназначены для *datasource_table*, а два — для *project_table*. Столбец *LastModifytime* используется для секционирования данных в таблице *datasource_table* из базы данных-источника. Запрос, используемый для чтения первой секции, — "SELECT * FROM datasource_table WHERE LastModifytime >= ' ' 2015-01-01 00:00:00 ' ' and LastModifytime <= ' ' 2015-12-31 23:59:59.999 ' ' '. Аналогичный запрос можно использовать для чтения данных из других секций.

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

2. Перейдите к шаблону " **групповое копирование из базы данных** ". Создайте **новое** соединение с таблицей внешнего элемента управления, созданной на шаге 1.

    ![Создание подключения к контрольной таблице](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Создайте **новое** соединение с базой данных-источником, из которой выполняется копирование данных.

    ![Создание подключения к базе данных-источнику](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Создайте **новое** подключение к целевому хранилищу данных, куда копируются данные.

    ![Создание подключения к целевому хранилищу](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Выберите **Использовать этот шаблон**.

6. Вы увидите конвейер, как показано в следующем примере:

    ![Проверка конвейера](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Выберите **Отладка**, введите **Параметры**и нажмите кнопку **Готово**.

    ![Щелкните * * Отладка * *](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Отобразятся результаты, аналогичные приведенным в следующем примере:

    ![Просмотр результатов](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. Используемых Если в качестве назначения данных выбрано "Azure синапсе Analytics (ранее SQL DW)", необходимо ввести подключение к хранилищу BLOB-объектов Azure для промежуточного хранения в соответствии с требованиями polybase хранилища данных SQL. Шаблон автоматически создаст путь к контейнеру для хранилища BLOB-объектов. Проверьте, создан ли контейнер после выполнения конвейера.
    
    ![Настройка Polybase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Дальнейшие действия

- [Знакомство с Фабрикой данных Azure](introduction.md)
