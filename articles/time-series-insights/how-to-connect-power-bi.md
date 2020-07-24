---
title: Подключение среды к Power BIам Azure Time Series Insights | Документация Майкрософт
description: Узнайте, как подключить службу "аналитика временных рядов Azure", чтобы Power BI для совместного использования, создания диаграмм и отображения данных в Организации.
author: deepakpalled
ms.author: dpalled
manager: diviso
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: b9d91921fc375a1209e8fa8df6e3c6ff56e55be0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046700"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Визуализация данных из службы "аналитика временных рядов Azure" в Power BI

Служба "аналитика временных рядов Azure" — это платформа для хранения, управления, запроса и визуализации данных временных рядов в облаке. [Power BI](https://powerbi.microsoft.com) — это средство бизнес-аналитики с богатыми возможностями визуализации, которое позволяет обмениваться ценными сведениями и результатами в Организации. Обе службы теперь можно интегрировать, чтобы получить лучшее из встроенных возможностей визуализации "аналитика временных рядов Azure", а также Power BI.

Вы узнаете, как:

* Подключение службы "аналитика временных рядов Azure" к Power BI с помощью облачного соединителя
* Создание визуальных элементов с помощью данных в Power BI
* Публикация отчета в Power BI и совместное использование с остальной частью Организации

По окончании вы узнаете, как визуализировать данные временных рядов с помощью службы "аналитика временных рядов Azure" и улучшать их с помощью надежной визуализации данных и возможностей предоставления общего доступа к Power BI.

Обязательно подпишитесь на [бесплатную подписку Azure](https://azure.microsoft.com/free/) , если у вас ее еще нет.

## <a name="prerequisites"></a>Обязательные условия

* Скачайте и установите последнюю версию [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Или создайте [среду Gen2 "аналитика временных рядов Azure](time-series-insights-update-how-to-manage.md) ".

> [!IMPORTANT]
>
> * Соединитель в настоящее время поддерживается в средах Azure Time Series Insights Gen2, настроенных **только для горячего хранения**.
> * Если у вас есть гостевой доступ к среде Gen2 "аналитика временных рядов Azure" из другого клиента Azure AD, доступ к соединителю будет невозможен. Ознакомьтесь с [политиками доступа к среде](./concepts-access-policies.md).

## <a name="connect-data-from-azure-time-series-insights-to-power-bi"></a>Подключение данных из службы "аналитика временных рядов Azure" к Power BI

Чтобы подключить среду службы "аналитика временных рядов Azure" к Power BI, выполните следующие действия.

1. Открытие обозревателя службы "аналитика временных рядов Azure"
1. Экспорт данных в виде запроса или необработанных данных
1. открывать Power BI Desktop;
1. Загрузка из пользовательского запроса

### <a name="export-data-into-power-bi-desktop"></a>Экспорт данных в Power BI Desktop

Чтобы начать работу:

1. Откройте обозреватель "аналитика временных рядов Azure" Gen2 Explorer и проучите данные.
1. После создания представления, с которым вы удовлетворены, перейдите в раскрывающееся меню **More Actions (дополнительные действия** ) и выберите **подключиться к Power BI**.

    [![Экспорт обозревателя "аналитика временных рядов Azure Gen2"](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Задайте параметры на этой вкладке:

   1. Укажите относительный интервал времени для просмотра. Если вы довольны существующим представлением, оставьте его в качестве **существующего времени**.
   
   1. Выберите между **агрегированными** и **необработанными событиями**. 
   
       > [!NOTE]
       > Вы всегда можете выполнять статистическую обработку данных позже в Power BI, но нельзя вернуться к необработанным данным после статистической обработки. 
       
       > [!NOTE]
       > Существует ограничение числа событий 250 000 для необработанных данных на уровне событий.

       [![Подключение](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Если вы не настроили среду "аналитика временных рядов Azure" с помощью " **горячего" хранилища**, вы получите предупреждение.

       [![Предупреждение о горячем хранении](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Вы можете настроить существующий экземпляр для **горячего хранения** в портал Azure.

1. Выберите **Копировать запрос в буфер обмена**.
1. Теперь запустите Power BI Desktop.
1. На вкладке **Главная** в Power BI Desktop выберите **получить данные** в левом верхнем углу, а затем **еще**.

    [![Раскрывающийся список домашней страницы](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Выполните поиск по запросу " **аналитика временных рядов Azure**", выберите **"аналитика временных рядов Azure" (бета-версия)**, а затем **подключитесь**.

    [![Подключение Power BI к службе "аналитика временных рядов Azure"](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Кроме того, перейдите на вкладку **Azure** , выберите **Azure Time Series Insights (бета-версия)**, а затем **подключитесь**.
    
1. Откроется диалоговое окно сообщения с запросом на разрешение на подключение к ресурсам сторонних производителей. Нажмите кнопку **продолжить**.

    [![Выбор создания настраиваемого запроса](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. В раскрывающемся меню в разделе **источник данных**выберите **создать настраиваемый запрос**. Вставьте из буфера обмена в поле необязательный **настраиваемый запрос (необязательно)** ниже, а затем нажмите кнопку **ОК**.

    [![Передайте пользовательский запрос и нажмите кнопку ОК.](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. Теперь таблица данных будет загружена. Нажмите кнопку **загрузить** , чтобы загрузить Power BI.

    [![Проверьте загруженные данные в таблице и выберите Load (загрузить).](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Если вы выполнили эти действия, перейдите к следующему разделу.

## <a name="create-a-report-with-visuals"></a>Создание отчета с визуальными элементами

Теперь, когда данные импортированы в Power BI, пришло время создать отчет с визуальными элементами.

1. Убедитесь, что в левой части окна выбрано представление **отчета** .

    [![Выбор представления отчетов](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  В столбце **визуализации** выберите нужный визуальный элемент. Например, выберите **график**. В холст будет добавлена пустая графика.

1.  В списке **поля** выберите **_Timestamp** и перетащите его в поле **ось** , чтобы отобразить элементы вдоль оси X. Не забудьте переключиться на **_Timestamp** в качестве значения для **оси** (по умолчанию — **иерархия дат**).

    [![Выбор представления отчетов](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  Опять же, в списке **поля** выберите **тимесериесид** и перетащите его в поле **значения** , чтобы отобразить элементы вдоль оси Y.

    [![Создание графика](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Чтобы добавить на холст другую диаграмму, выберите в любом месте холста за пределами линейного графика и повторите эту процедуру.

    [![Создание дополнительных диаграмм для совместного использования](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

После создания отчета его можно опубликовать в Power BI Reporting Services.

## <a name="advanced-editing"></a>Расширенное редактирование

Если вы уже загрузили набор данных в Power BI, но хотите изменить запрос (например, параметры даты и времени или идентификатора среды), это можно сделать с помощью функций Расширенный редактор Power BI. Дополнительные сведения см. в [документации по Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview) .

Общие сведения:

1. В Power BI Desktop выберите **изменить запросы**.
1. Нажмите **Расширенный редактор**.

    [![Изменение запросов в Расширенный редактор](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. При необходимости измените полезные данные JSON.
1. Нажмите кнопку **Готово** , а затем **закройте & применить** в **окне редактора Power Query**.

Теперь интерфейс будет отражать примененные необходимые изменения.  

## <a name="next-steps"></a>Дальнейшие шаги

* Узнайте о [концепциях соединителей Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview) для Azure Time Series Insights.

* Дополнительные сведения о [Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-query-overview).
