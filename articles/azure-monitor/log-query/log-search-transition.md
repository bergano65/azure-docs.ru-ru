---
title: Просмотр и анализ данных в Azure Log Analytics | Документация Майкрософт
description: Помощь для пользователей, переходящих с поиска по журналам Log Analytics на использование запросов журналов Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: bwren
ms.openlocfilehash: dc3a754bd1a9f689f4b24b9ee2c1e4cbc0611684
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55751367"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Переход с поиска по журналам Log Analytics на анализ журналов Azure Monitor
Поиск по журналам в Log Analytics недавно был заменен новым интерфейсом для анализа журналов Azure Monitor. Страница поиска по журналам по-прежнему доступна через пункт меню **Журналы (классические)** на странице **Рабочие области Log Analytics** портала Azure, но она будет удалена 15 февраля 2019 г. В этой статье описываются различия между двумя интерфейсами, чтобы помочь вам перейти с поиска по журналам. 

## <a name="filter-results-of-a-query"></a>Фильтрация результатов запроса
В поиске по журналам список фильтров отображается, когда возвращаются результаты поиска. Выберите фильтр и щелкните **Применить** для выполнения запроса с выбранным фильтром.

![Фильтр поиска по журналам](media/log-search-transition/filter-log-search.png)

В журналах Azure Monitor выберите *Filter (preview)* (Фильтр (предварительная версия)) для отображения фильтров. Щелкните значок фильтра, чтобы отобразить дополнительные фильтры. Выберите фильтр и щелкните **Apply & Run** (Применить и выполнить) для выполнения запроса с выбранным фильтром.

![Фильтр журналов](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Извлечение настраиваемых полей 
В поиске по журналам вы извлекаете [настраиваемые поля](../platform/custom-fields.md) из представления списка, где меню поля включает в себя действие _Извлечь поля из "название таблицы"_.

![Извлечение полей в интерфейсе поиска по журналам](media/log-search-transition/extract-fields-log-search.png)

В журналах Azure Monitor настраиваемые поля извлекаются из представления таблицы. Разверните запись, щелкнув стрелку слева от нее, а затем щелкните многоточие, чтобы получить доступ к действию _Извлечь поля из "название таблицы"_.

![Извлечение полей журналов](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Функции и группы компьютеров
Чтобы сохранить результаты поиска в поиске по журналам, выберите **Сохраненные условия поиска** и **Добавить**, чтобы указать имя, категорию и текст запроса. Создайте [группу компьютеров](../platform/computer-groups.md), добавив псевдоним функции.

![Сохранение поиска по журналам](media/log-search-transition/save-search-log-search.png)

Чтобы сохранить текущий запрос в журналах Azure Monitor, выберите **Сохранить**. В поле **Сохранить как** выберите значение _Функция_ и укажите **псевдоним функции** для создания [функции](functions.md). Щелкните _Сохранить этот запрос как группу компьютеров_, чтобы использовать псевдоним функции для [группы компьютеров](../platform/computer-groups.md).

![Сохранение запроса к журналу](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Сохраненные запросы
В поиске по журналам ваши сохраненные запросы доступны через элемент **Сохраненные условия поиска** на панели действий. В журналах Azure Monitor получите доступ к сохраненным запросам из [обозревателя запросов](../log-query/get-started-portal.md#save-queries).

![Обозреватель запросов](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Детализация сводных строк
В поиске по журналам вы можете щелкнуть строку в сводном запросе, чтобы запустить другой запрос, в котором перечислены подробные записи этой строки.

![Детализация поиска по журналам](media/log-search-transition/drilldown-search.png)

В журналах Azure Monitor необходимо изменить запрос, чтобы возвращать эти записи. Разверните одну из строк в результатах и щелкните **+** рядом со значением, чтобы добавить значение в запрос. Затем закомментируйте команду **summarize** и повторите запрос.

![Детализация журналов Azure Monitor](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Выполнить действие
В поиске по журналам вы можете [запускать модуль runbook](take-action.md) из результата поиска, выбрав **Выполнить действие**.

![Выполнить действие](media/log-search-transition/take-action-log-search.png)

В журналах Azure Monitor [создайте оповещение из запроса к журналу](../platform/alerts-log.md). Настройте группу действий с одним или несколькими действиями, которые будут выполняться в ответ на оповещение.

![Группа действий](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения см. в статье [Начало работы с Log Analytics на портале Azure](get-started-portal.md).