---
title: Скрипты SQL в Azure синапсе Studio (Предварительная версия)
description: Введение в скрипты SQL для Azure синапсе Studio (Предварительная версия)
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: ee384d6095ccbf25225a435fe8afe4281c5d62df
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921397"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Использование скрипта SQL в Azure синапсе Studio (Предварительная версия)

Azure синапсе Studio (Предварительная версия) предоставляет веб-интерфейс скрипта SQL для создания запросов SQL. Вы можете подключиться к пулу SQL (Предварительная версия) или SQL по запросу (Предварительная версия). 

## <a name="begin-authoring-in-sql-script"></a>Начало разработки в скрипте SQL 

Существует несколько способов запуска процесса разработки в скрипте SQL. Создать новый скрипт SQL можно одним из следующих способов.

1. В меню «Разработка» выберите значок **«+»** и щелкните « **скрипт SQL**».

    ![невсклскрипт](media/author-sql-script/newsqlscript.png)

2. В меню **действия** выберите команду **создать скрипт SQL**.
    
    ![newsqlscript2actions](media/author-sql-script/newsqlscript2actions.png)

Кроме того, можно сделать следующее. 

3. Выберите **Импорт** в меню **действия** в разделе Разработка скриптов SQL и выберите существующий скрипт SQL из локального хранилища.
 
    ![newsqlscript3actions](media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Создание скрипта SQL

1. Выберите имя для скрипта SQL, нажав кнопку **Свойства** и заменив имя по умолчанию, присвоенное скрипту SQL.
  
    ![невсклскриптренаме](media/author-sql-script/newsqlscriptrename.png)

2. Выберите конкретный пул SQL или SQL по запросу из раскрывающегося меню **Подключение к** . При необходимости выберите базу данных из **базы данных**.
 
    ![невсклчусепул](media/author-sql-script/newsqlchoosepool.png)

3. Приступите к созданию скрипта SQL с помощью функции IntelliSense.

    ![невсклинтеллисенсе](media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Выполнение скрипта SQL

Нажмите кнопку **выполнить** , чтобы выполнить скрипт SQL. Результаты по умолчанию отображаются в таблице.

![невсклскриптресултстабле](media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Экспорт результатов

Результаты можно экспортировать в локальное хранилище в различных форматах (включая CSV, Excel, JSON, XML), выбрав "Экспорт результатов" и выбрав расширение.

Можно также визуализировать результаты скрипта SQL на диаграмме, нажав кнопку **Диаграмма** . Выберите столбец Тип диаграммы и **Категория**. Вы можете экспортировать диаграмму в рисунок, выбрав **Сохранить как изображение**. 

![невсклскриптресултсчарт](media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Просмотр данных из файла Parquet

Вы можете исследовать файлы Parquet в учетной записи хранения с помощью скрипта SQL, чтобы просмотреть содержимое файла.

![невскриптсклодпаркует](media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Таблицы SQL, внешние таблицы, представления

Выбрав меню **действия** в разделе данные, можно выбрать несколько действий, например:

- Новый скрипт SQL
- Выбрать первые 1000 строк
- CREATE
- УДАЛИТЬ и создать 
 
Просмотрите доступный жест, щелкнув правой кнопкой мыши узлы пула SQL и SQL по запросу.
 
![невскриптдатабасе](media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о создании скрипта SQL см. в статье [Azure синапсе Analytics](https://docs.microsoft.com/azure/synapse-analytics).
