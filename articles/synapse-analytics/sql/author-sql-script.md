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
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431076"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Использование скрипта SQL в Azure синапсе Studio (Предварительная версия)

Azure синапсе Studio (Предварительная версия) предоставляет веб-интерфейс скрипта SQL для создания запросов SQL. Вы можете подключиться к пулу SQL (Предварительная версия) или SQL по запросу (Предварительная версия). 

## <a name="begin-authoring-in-sql-script"></a>Начало разработки в скрипте SQL 

Существует несколько способов запуска процесса разработки в скрипте SQL. Создать новый скрипт SQL можно одним из следующих способов.

1. Щелкните значок "+" и выберите Скрипт SQL.

    > [!div class="mx-imgBorder"] 
    >![невсклскрипт](./media/author-sql-script/newsqlscript.png)

2. В меню действия в разделе Разработка скриптов SQL выберите пункт "создать скрипт SQL" в меню "действия" в разделе "Разработка скриптов SQL". 

    > [!div class="mx-imgBorder"] 
    > ![невсклскрипт](./media/author-sql-script/newsqlscript2actions.png)

или диспетчер конфигурации служб 

3. Выберите "Импорт" в меню "действия" в разделе "Разработка скриптов SQL" и выберите существующий скрипт SQL из локального хранилища.

    > [!div class="mx-imgBorder"] 
    > ![невсклскрипт](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Создание скрипта SQL

1. Выберите имя для скрипта SQL, нажав кнопку "свойство" и заменив имя по умолчанию, присвоенное сценарию SQL.

    > [!div class="mx-imgBorder"] 
    > ![невсклскрипт](./media/author-sql-script/newsqlscriptrename.png)

1. Выберите в раскрывающемся меню "подключение к" конкретный пул SQL или SQL по запросу. При необходимости выберите базу данных в поле "использовать базу данных".

    > [!div class="mx-imgBorder"] 
    > ![невсклскрипт](./media/author-sql-script/newsqlchoosepool.png)

1. Приступите к созданию скрипта SQL с помощью функции IntelliSense.

    > [!div class="mx-imgBorder"] 
    > ![невсклскрипт](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Выполнение скрипта SQL

Нажмите кнопку "выполнить", чтобы выполнить скрипт SQL. Результаты по умолчанию отображаются в таблице.

> [!div class="mx-imgBorder"] 
> ![невсклскрипт](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Экспорт результатов

Результаты можно экспортировать в локальное хранилище в различных форматах (включая CSV, Excel, JSON, XML), выбрав "Экспорт результатов" и выбрав расширение.

Можно также визуализировать результаты скрипта SQL в диаграмме, нажав кнопку "Диаграмма". Выберите "тип диаграммы" и "столбец категории". Можно экспортировать диаграмму в рисунок, выбрав команду "Сохранить как изображение". 

> [!div class="mx-imgBorder"] 
> ![невсклскрипт](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Просмотр данных из файла Parquet.

Вы можете исследовать файлы Parquet в учетной записи хранения с помощью скрипта SQL, чтобы просмотреть содержимое файла. 

> [!div class="mx-imgBorder"] 
> ![невсклскрипт](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Таблицы SQL, внешние таблицы, представления

Выбрав меню "действия" в разделе "данные", можно выбрать несколько таких действий: "создать скрипт SQL", "выбрать первые 1000 строк", "создать", "удалить и создать". Просмотрите доступный жест, щелкнув правой кнопкой мыши узлы пула SQL и SQL по запросу.

> [!div class="mx-imgBorder"] 
> ![невсклскрипт](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о создании скрипта SQL см. в статье [Azure синапсе Analytics](https://docs.microsoft.com/azure/synapse-analytics).