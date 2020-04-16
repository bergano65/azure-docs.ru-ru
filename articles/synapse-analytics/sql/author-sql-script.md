---
title: Скрипты СЗЛ в студии Azure Synapse (предварительный просмотр)
description: Введение Azure Synapse Studio (предварительный просмотр) скриптов S'L
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431076"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>Использование скрипта S'L в студии Azure Synapse (предварительный просмотр)

Azure Synapse Studio (предварительный просмотр) предоставляет веб-интерфейс скрипта для автора запросов. Вы можете подключиться к пулу S'L (предварительный просмотр) или по запросу (предварительный просмотр). 

## <a name="begin-authoring-in-sql-script"></a>Начало авторизации в скрипте S'L 

Существует несколько способов запуска авторского опыта в скрипте S'L. Вы можете создать новый скрипт S'L с помощью одного из следующих методов.

1. Выберите значок «К» и выберите скрипт S'L.

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. Из меню «Действия» под разработкой сценариев S'L выберите «Новый скрипт» из меню «Действия» под сценарием «Разработка» в рамках разработки скриптов. 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

or 

3. Выберите "Импорт" из меню "Действия" в соответствии со сценариями разработки S'L и выберите существующий скрипт S'L из локального хранилища.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Создайте свой скрипт S'L

1. Выберите имя для скрипта S'L, выбрав кнопку «Свойство» и заменив имя по умолчанию, назначенное скрипту S'L.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Выберите конкретный пул S'L или s-L по требованию из меню «Подключение к» выпадающим. Или при необходимости выберите базу данных из "Использовать базу данных".

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Начните наполнять свой скрипт S'L с помощью функции intellisense.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Запустите скрипт S'L

Выберите кнопку "Бег" для выполнения сценария S'L. Результаты отображаются по умолчанию в таблице.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Экспортировать свои результаты

Результаты можно экспортировать в локальное хранилище в различных форматах (включая CSV, Excel, JSON, XML), выбрав "Результаты экспорта" и выбрав расширение.

Вы также можете визуализировать результаты сценария S'L на диаграмме, выбрав кнопку "Chart". Выберите "Тип диаграммы" и "Колонка категории". Вы можете экспортировать диаграмму в картинку, выбрав "Сохранить как изображение". 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Исследуйте данные из файла Паркета.

Вы можете исследовать файлы Паркета в учетной записи хранилища с помощью сценария S'L для предварительного просмотра содержимого файла. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>Таблицы СЗЛ, внешние таблицы, представления

Выбрав меню «Действия» по данным, можно выбрать несколько действий, таких как: «Новый скрипт S'L», «Выберите топ-1000 строк», «CREATE», «DROP и CREATE». Изучите доступный жест, нажав на узлы бассейна S'L и S'L по требованию.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>Дальнейшие действия

Для получения более подробной информации [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)об авторе сценария S'L см.