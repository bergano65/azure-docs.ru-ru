---
title: Скрипты SQL в Azure синапсе Studio (Предварительная версия)
description: Введение в скрипты SQL для Azure синапсе Studio (Предварительная версия)
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 3f3009799889bd6b118f586676e22338d821d37c
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2020
ms.locfileid: "94635295"
---
# <a name="using-sql-scripts-in-azure-synapse-studio-preview"></a>Использование скриптов SQL в Azure синапсе Studio (Предварительная версия)

Azure синапсе Studio (Предварительная версия) предоставляет веб-интерфейс скрипта SQL для создания запросов SQL. Вы можете подключиться к пулу SQL (Предварительная версия). 

## <a name="begin-authoring-in-sql-script"></a>Начало разработки в скрипте SQL 

Существует несколько способов запуска процесса разработки в скрипте SQL. Создать новый скрипт SQL можно одним из следующих способов.

1. В меню «Разработка» выберите значок **«+»** и щелкните « **скрипт SQL** ».

2. В меню **действия** выберите команду **создать скрипт SQL**.

3. Выберите **Импорт** в меню **действия** в разделе Разработка скриптов SQL. Выберите существующий скрипт SQL из локального хранилища.
![новые действия SQL Script 3](media/author-sql-script/new-sql-script-3-actions.png)

## <a name="create-your-sql-script"></a>Создание скрипта SQL

1. Выберите имя для скрипта SQL, нажав кнопку **Свойства** и заменив имя по умолчанию, присвоенное скрипту SQL. 
![Переименование нового скрипта SQL](media/author-sql-script/new-sql-script-rename.png)

2. Выберите конкретный выделенный пул SQL или бессерверный пул SQL из раскрывающегося меню **Подключение к** . При необходимости выберите базу данных из **базы данных**. 
![создать SQL выбор пула](media/author-sql-script/new-sql-choose-pool.png)

3. Приступите к созданию скрипта SQL с помощью функции IntelliSense.

## <a name="run-your-sql-script"></a>Выполнение скрипта SQL

Нажмите кнопку **выполнить** , чтобы выполнить скрипт SQL. Результаты по умолчанию отображаются в таблице.

![Таблица результатов создания скрипта SQL](media/author-sql-script/new-sql-script-results-table.png)

## <a name="export-your-results"></a>Экспорт результатов

Результаты можно экспортировать в локальное хранилище в различных форматах (включая CSV, Excel, JSON, XML), выбрав "Экспорт результатов" и выбрав расширение.

Можно также визуализировать результаты скрипта SQL на диаграмме, нажав кнопку **Диаграмма** . Выберите столбец Тип диаграммы и **Категория**. Вы можете экспортировать диаграмму в рисунок, выбрав **Сохранить как изображение**. 

![Диаграмма результатов создания скрипта SQL](media/author-sql-script/new-sql-script-results-chart.png)

## <a name="explore-data-from-a-parquet-file"></a>Просмотр данных из файла Parquet

Вы можете исследовать файлы Parquet в учетной записи хранения с помощью скрипта SQL, чтобы просмотреть содержимое файла.

![новый скрипт склод Parquet](media/author-sql-script/new-script-sqlod-parquet.png)

## <a name="sql-tables-external-tables-views"></a>Таблицы SQL, внешние таблицы, представления

Выбрав меню **действия** в разделе данные, можно выбрать несколько действий, например:

- Новый скрипт SQL
- Выбрать первые 1000 строк
- CREATE
- УДАЛИТЬ и создать 
 
Изучите доступные жесты, щелкнув правой кнопкой мыши узлы баз данных SQL.
 
![создать базу данных скриптов](media/author-sql-script/new-script-database.png)

## <a name="create-folders-and-move-sql-scripts-into-a-folder"></a>Создание папок и перемещение скриптов SQL в папку

В меню действия в разделе Разработка скриптов SQL выберите пункт "создать папку" в меню "действия" в разделе "Разработка скриптов SQL". И введите имя новой папки во всплывающем окне. 

> [!div class="mx-imgBorder"] 
> ![невсклскрипт](./media/author-sql-script/new-sql-script-create-folder.png)

Чтобы переместить скрипт SQL в папку, можно выбрать скрипт SQL и выбрать пункт "переместить в" в меню "действия". Затем найдите конечную папку в новом окне и переместите скрипт SQL в выбранную папку. Можно также быстро перетащить скрипт SQL и поместить его в папку.  

> [!div class="mx-imgBorder"] 
> ![невсклскрипт](./media/author-sql-script/new-sql-script-move-folder.png)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о создании скрипта SQL см. в статье [Azure синапсе Analytics](https://docs.microsoft.com/azure/synapse-analytics).
