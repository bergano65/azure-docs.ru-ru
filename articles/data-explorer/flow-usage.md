---
title: Примеры использования разъема потока исследователя данных Microsoft Azure (Preview)
description: Изучите несколько общих примеров использования разъема Microsoft Flow.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 796b37f98fed7e389fa71a15b5e6697a14db1a16
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397213"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>Примеры использования разъема потока Microsoft Flow (Preview)

Разъем потока Azure Data Explorer позволяет Azure Data Explorer использовать [возможности потока Microsoft Power Automate](https://flow.microsoft.com/) для автоматического выполнения запросов и команд Kusto в рамках запланированной или срабатывающей задачи. Этот документ содержит несколько общих примеров использования разъема Microsoft Flow.

Для получения дополнительной [Microsoft Flow connector (Preview)](flow.md)информации см.

* [Разъем и сот](#microsoft-flow-connector-and-sql)
* [Нажмите данные на набор данных Power BI](#push-data-to-power-bi-dataset)
* [Условные запросы](#conditional-queries)
* [Отправьте по электронной почте несколько диаграмм Azure Data Explorer Flow](#email-multiple-azure-data-explorer-flow-charts)
* [Отправить другое письмо различным контактам](#send-a-different-email-to-different-contacts)
* [Создание пользовательской таблицы HTML](#create-a-custom-html-table)

## <a name="microsoft-flow-connector-and-sql"></a>Разъем и сот

Используйте разъем Microsoft Flow для запроса данных и их агрегирования в базе данных S'L.

> [!Note]
> Вставка S'L выполняется отдельно для каждой строки. Используйте разъем Microsoft Flow только для небольших объемов выходных данных. 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> В поле *названия кластера* введите URL кластера.

## <a name="push-data-to-power-bi-dataset"></a>Нажмите данные на набор данных Power BI

Разъем Microsoft Flow можно использовать вместе с разъемом Power BI для продвижения данных из запросов Kusto в наборы потоковых данных Power BI.

1. Создайте новое действие запроса run и list results.
1. Выберите **новый шаг**.
1. Выберите **Добавить действие** и поиск Power BI.
1. Выберите **Power BI**.
1. Выберите **строки добавления в набор данных.** 

    ![Разъем Flow Power BI](./media/flow-usage/flow-powerbiconnector.png)
1. Введите **рабочее пространство,** **набор данных**и **таблицу,** в которую будут перемещены данные.
1. Из диалога динамического содержимого добавьте полезную нагрузку, содержащую схему набора данных и соответствующие результаты запроса Kusto.

    ![Поля Power BI потока](./media/flow-usage/flow-powerbifields.png)

Поток автоматически применяет действие Power BI для каждой строки таблицы результатов запроса Kusto. 

![Действие Flow Power BI для каждой строки](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>Условные запросы

Результаты запросов Kusto могут быть использованы в качестве входных данных или условий для следующих действий потока.

В следующем примере мы запросим Kusto для инцидентов, которые произошли в течение последнего дня. Для каждого разрешенного инцидента публикуется неустойное сообщение и создается push-уведомление.
Для каждого инцидента, который все еще активен, Kusto запрашивается для получения дополнительной информации о подобных инцидентах. Он отправляет эту информацию в виде электронной почты и открывает соответствующую задачу TFS.

Следуйте этим инструкциям, чтобы создать аналогичный поток:

1. Создайте новое действие запроса run и list results.
1. Выберите **новый шаг**.
1. Выберите **элемент управления состоянием.**
1. Из окна динамического содержимого выберите параметр, который вы хотите использовать в качестве условия для следующих действий.
1. Выберите тип *отношения* и *значение,* чтобы установить определенное условие по данному параметру.

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    Поток применяет это условие в каждой строке таблицы результатов запроса.
1. Добавляйте действия, когда условие верно и ложно.

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

Значения результатов из запроса Kusto можно использовать в качестве ввода для следующих действий. Выберите значения результата из окна динамического содержимого.
В приведенном ниже примере были добавлены действия Slack - Post Message и Visual Studio - Создание нового элемента работы, содержащего данные из запроса Kusto.

![Slack - Действие сообщения](./media/flow-usage/flow-slack.png)

![Действие визуальной студии](./media/flow-usage/flow-visualstudio.png)

В этом примере, если инцидент все еще активен, необходимо снова задать запрос Kusto, чтобы получить информацию о том, как инциденты из того же источника были решены в прошлом.

![Запрос состояния потока](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> В поле *названия кластера* введите URL кластера.

Визуализируйте эту информацию как круговую диаграмму и отправьте ее команде по электронной почте.

![Сообщение о состоянии потока](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>Отправьте по электронной почте несколько диаграмм Azure Data Explorer Flow

1. Создайте новый поток с триггером "Повторение" и определите интервал потока и частоту. 
1. Добавьте новый шаг, с одним или более Kusto - Выполнить запрос и визуализировать результаты действий. 

    ![Выполнить несколько запросов в потоке](./media/flow-usage/flow-severalqueries.png)
1. Для каждого Kusto - Выполнить запрос и визуализировать результат, определить следующие поля:
    * URL кластера (в поле *названия кластера)*
    * Имя базы данных
    * Тип запроса и диаграммы (HTML Таблица/ Pie Chart/ Time Chart/ Bar Chart/ Enter Custom Value).

    ![Визуализация результатов с несколькими вложениями](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

    > [!IMPORTANT]
    > В полях *названия кластера* введите URL кластера.

1. Добавить действие Отправить по электронной почте. 
    * В поле *тела* вставьте требуемое тело так, чтобы визуализированный результат запроса был включен в тело электронной почты.
    * Чтобы добавить вложение в электронную почту, добавьте имя приложения и содержимое приложения.
    
    ![Отправить несколько вложений по электронной почте](./media/flow-usage/flow-email-multiple-attachments.png)

    Полные инструкции по созданию действия по электронной почте можно узнать в [результатах запроса Email Kusto.](flow.md#email-kusto-query-results) 

Результат:

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="send-a-different-email-to-different-contacts"></a>Отправить другое письмо различным контактам

Вы можете использовать Microsoft Flow для отправки различных заказных писем различным контактам. Адреса электронной почты и содержимое электронной почты являются результатом запроса Kusto.

Пример

![Динамическая электронная почта с использованием запроса Kusto](./media/flow-usage/flow-dynamicemailkusto.png)

> [!IMPORTANT]
> В поле *названия кластера* введите URL кластера.

![Динамическая электронная почта в действии потока](./media/flow-usage/flow-dynamicemail.png)

## <a name="create-a-custom-html-table"></a>Создание пользовательской таблицы HTML

Вы можете использовать Microsoft Flow для создания и использования пользовательских HTML элементов, таких как пользовательская таблица HTML.

Ниже приводится следующий пример, как создать пользовательскую таблицу HTML. Таблица HTML будет иметь свои строки, окрашенные на уровень журнала (так же, как в Azure Data Explorer).

Следуйте этим инструкциям, чтобы создать аналогичный поток:

1. Создайте новое действие Kusto - Run query и список результатов.

    ![Список результатов для таблицы HTML](./media/flow-usage/flow-listresultforhtmltable.png)

> [!IMPORTANT]
> В поле *названия кластера* введите URL кластера.

1. Петля над результатами запроса и создание тела таблицы HTML: 
    1. Чтобы создать переменную для удержания строки HTML, выберите **Новый шаг**
    1. Выберите **Добавить действие** и искать переменные. 
    1. Выберите **переменные - Инициализация переменной**. 
    1. Инициализируем переменную строки следующим образом:

    ![Инициализация переменной](./media/flow-usage/flow-initializevariable.png)

1. Петля над результатами:
    1. Выберите **новый шаг**.
    1. Выберите **Добавить действие.**
    1. Поиск переменных. 
    1. Выберите **переменные - Приложение к переменной строки**. 
    1. Выберите имя переменной, которое вы инициализировали ранее, и создайте строки таблиц HTML с помощью результатов запроса. 
    При выборе результатов запроса автоматически добавляется обращение к каждому из них.

    В приведенном ниже `if` примере выражение используется для определения стиля каждой строки:

    ```if(equals(items('Apply_to_each')?['Level'], 'Warning'), 'Yellow', if(equals(items('Apply_to_each')?['Level'], 'Error'), 'red', 'white'))```

    [![](./media/flow-usage/flow-createhtmltableloopcontent.png "Create HTML table loop content")](./media/flow-usage/flow-createhtmltableloopcontent.png#lightbox)

1. Создайте полный HTML-контент: 
    1. Добавьте новое действие за пределами Apply к каждому из них. 
    В следующем примере используется действие Отправить электронное письмо.
    1. Определите свою таблицу HTML, используя переменную от предыдущих шагов. 
    1. Если вы отправляете электронное письмо, выберите **предварительные опции и,** под is HTML, выберите **«Да».**

    ![Пользовательская электронная почта таблицы HTML](./media/flow-usage/flow-customhtmltablemail.png)

Результат:

![Пользовательский результат электронной почты таблицы HTML](./media/flow-usage/flow-customhtmltableresult.png)

## <a name="next-steps"></a>Следующие шаги

Узнайте о [разъеме Microsoft Azure Explorer Logic App,](https://docs.microsoft.com/azure/kusto/tools/logicapps) который является еще одним способом автоматического выполнения запросов и команд Kusto в рамках запланированной или срабатывающей задачи.
