---
title: Подключение среды к Power BIам Azure Time Series Insights | Документация Майкрософт
description: Узнайте, как подключить службу "аналитика временных рядов Azure", чтобы Power BI для совместного использования, создания диаграмм и отображения данных в Организации.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 8a464a8a635e0531eba2f9cc6fbceeb9edcea8bb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705973"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Визуализация данных из анализа временных рядов в Power BI

Служба "аналитика временных рядов Azure" — это платформа для хранения, управления, запроса и визуализации данных временных рядов в облаке. [Power BI](https://powerbi.microsoft.com) — это средство бизнес-аналитики с богатыми возможностями визуализации, которое позволяет обмениваться ценными сведениями и результатами в Организации. Обе службы теперь можно интегрировать, чтобы получить лучшее из встроенных функций визуализации временных рядов, а также Power BI.

Вы узнаете, как:

* Подключение Time Series Insights к Power BI с помощью облачного соединителя
* Создание визуальных элементов с помощью данных в Power BI
* Публикация отчета в Power BI и совместное использование с остальной частью Организации

По окончании вы узнаете, как визуализировать данные временных рядов с помощью службы "аналитика временных рядов Azure" и улучшать их с помощью надежной визуализации данных и возможностей предоставления общего доступа к Power BI.

Обязательно подпишитесь на [бесплатную подписку Azure](https://azure.microsoft.com/free/) , если у вас ее еще нет.

## <a name="prerequisites"></a>Технические условия

* Скачайте и установите последнюю версию [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Создание [экземпляра предварительной версии службы "аналитика временных рядов Azure](time-series-insights-update-how-to-manage.md) "

> [!IMPORTANT]
> Соединитель Power BI в настоящее время поддерживается в средах "аналитика временных рядов" Предварительная версия *"с оплатой по мере использования"* , настроенных для **горячего хранения**.

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Подключение данных из Time Series Insights к Power BI

Чтобы подключить среду "аналитика временных рядов" к Power BI, выполните следующие действия.

1. Открыть обозреватель аналитики временных рядов                      
1. Экспорт данных в виде запроса или необработанных данных                       
1. Открыть Power BI Desktop
1. Загрузка из пользовательского запроса

### <a name="export-data-into-power-bi-desktop"></a>Экспорт данных в Power BI Desktop

Чтобы приступить к работе, сделайте следующее:

1. Откройте обозреватель предварительного просмотра "аналитика временных рядов" и проучите данные.
1. После создания представления, с которым вы удовлетворены, перейдите в раскрывающееся меню **More Actions (дополнительные действия** ) и щелкните **подключиться к Power BI**. 

    [экспорт обозревателя предварительной версии аналитики временных рядов ![](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Задайте параметры на этой вкладке:

   1. Укажите относительный интервал времени для просмотра. Если вы довольны существующим представлением, оставьте его в качестве **существующего времени**. 
   1. Выберите между **агрегированными** и **необработанными событиями**. 
   
       > [!NOTE]
       > Вы всегда можете выполнять статистическую обработку данных позже в Power BI, но нельзя вернуться к необработанным данным после статистической обработки. 
       
       > [!NOTE]
       > Для данных на уровне необработанных событий существует ограничение в 100 КБ.

       [Подключение ![](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Если вы не настроили экземпляр "аналитика временных рядов" для " **горячего" хранилища**, вы получите предупреждение.

       [Подключение ![](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Вы можете настроить существующий экземпляр для **горячего хранения** в портал Azure.

1. Выберите **Копировать запрос в буфер обмена**.
1. Теперь запустите Power BI Desktop.
1. На вкладке **Главная** в Power BI Desktop выберите **получить данные** в левом верхнем углу, а затем **еще**.

    [раскрывающийся список ![Домашняя страница](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Выполните поиск по запросу " **аналитика временных рядов**", выберите **"аналитика временных рядов Azure" (бета-версия)** , а затем **подключитесь**.

    [![подключение Power BI к службе "аналитика временных рядов"](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Кроме того, перейдите на вкладку **Azure** , выберите **Azure Time Series Insights (бета-версия)** , а затем **подключитесь**.
    
1. Откроется диалоговое окно сообщения с запросом на разрешение на подключение к ресурсам сторонних производителей. Нажмите кнопку **продолжить**.

    [![выберите создать настраиваемый запрос.](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. В раскрывающемся меню в разделе **источник данных**выберите **создать настраиваемый запрос**. Вставьте из буфера обмена в поле необязательный **настраиваемый запрос (необязательно)** ниже, а затем нажмите кнопку **ОК**.

    [![передать пользовательский запрос и нажмите кнопку ОК.](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. Теперь таблица данных будет загружена. Нажмите кнопку **загрузить** , чтобы загрузить Power BI.

    [![проверьте загруженные данные в таблице и выберите Load (загрузить).](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Если вы выполнили эти действия, перейдите к следующему разделу.

## <a name="create-a-report-with-visuals"></a>Создание отчета с визуальными элементами

Теперь, когда данные импортированы в Power BI, пришло время создать отчет с визуальными элементами.

1. Убедитесь, что в левой части окна выбрано представление **отчета** .

    [![выбрать представление отчета](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  В столбце **визуализации** выберите нужный визуальный элемент. Например, выберите **график**. В холст будет добавлена пустая графика.
 
1.  В списке **поля** выберите **timestamp** и перетащите его в поле **ось** , чтобы отобразить элементы вдоль оси X.

1.  Опять же, в списке **поля** выберите **тимесериесид** и перетащите его в поле **значения** , чтобы отобразить элементы вдоль оси Y.

    [![создать график](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Чтобы добавить еще одну диаграмму на холст, щелкните в любом месте холста за пределами линейного графика и повторите эту процедуру.

    [![создать дополнительные диаграммы для совместного использования](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

После создания отчета его можно опубликовать в Power BI Reporting Services.

## <a name="advanced-editing"></a>Расширенное редактирование

Если вы уже загрузили набор данных в Power BI, но хотите изменить запрос (например, параметры даты и времени или идентификатора среды), это можно сделать с помощью функций Расширенный редактор Power BI. Дополнительные сведения см. в [документации по Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview) .

Общие сведения:

1. В Power BI Desktop выберите **изменить запросы**.
1. Нажмите **Расширенный редактор**.

    [![изменить запросы в Расширенный редактор](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. При необходимости измените полезные данные JSON.
1. Нажмите кнопку **Готово** , а затем **закройте & применить** в **окне редактора Power Query**.

Вы должны увидеть, что нужные изменения применены.  

## <a name="next-steps"></a>Следующие шаги

* Узнайте о [концепциях соединителей Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview) для Azure Time Series Insights.

* Дополнительные сведения о [Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-query-overview).

* См. раздел [Time Series Insights Explorer обозревателя "аналитика](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) временных рядов", [Обозреватель предварительной версии "аналитика временных рядов](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)".
