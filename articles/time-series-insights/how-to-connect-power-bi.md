---
title: Подключите среду к Power BI - Исследования серии времени Azure Документы Майкрософт
description: Узнайте, как подключить Исследования временных рядов Azure к Power BI для обмена, диаграммы и отображения данных в организации.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 22053bdc3a9836b76aa92303234a095cac6448ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863848"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Визуализация данных из Time Series Исследования в Power BI

Azure Time Series Insights — это платформа для хранения, управления, запроса и визуализации данных временных рядов в облаке. [Power BI](https://powerbi.microsoft.com) — это инструмент бизнес-аналитики с богатыми возможностями визуализации, который позволяет обмениваться мнениями и результатами в организации. Обе службы теперь могут быть интегрированы, чтобы получить лучшее из как Time Series Insights'присущие визуализации возможностей, а также Power BI.

Вы узнаете, как:

* Подключите Time Series Insights к Power BI с помощью облачного разъема
* Создавайте визуальные эффекты с помощью данных в Power BI
* Опубликовать отчет в Power BI и поделиться с остальной частью вашей организации

В конце концов, вы узнаете, как визуализировать данные временных рядов через Azure Time Series Insights и улучшить их с помощью мощной визуализации данных и простых возможностей совместного использования Power BI.

Убедитесь в том, чтобы подписаться на [бесплатную подписку Azure,](https://azure.microsoft.com/free/) если у вас ее еще нет.

## <a name="prerequisites"></a>Предварительные требования

* Скачать и установить последнюю версию [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Иметь или создавать [экземпляр Azure Time Series Исследования](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
> Разъем Power BI в настоящее время поддерживается в Time Series Исследования Предварительный *оплаты по мере вас-го* среды настроены для **теплый магазин**.

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Подключение данных из Time Series Insights к Power BI

Чтобы подключить среду Time Series Insights к Среде Power BI, выполните следующие действия:

1. Исследователь анализов серии «Открытое время»
1. Экспортные данные как запрос или как необработанные данные
1. Open Power BI Desktop
1. Загрузка из пользовательского запроса

### <a name="export-data-into-power-bi-desktop"></a>Экспортные данные на рабочий стол Power BI

Чтобы начать работу:

1. Откройте Time Series Insights Preview Explorer и курируйте ваши данные.
1. После того как вы создали представление, вас устраивает, перейдите в меню **больше действий** выпадения и выберите **Подключите к Power BI.**

    [![Время Серии Исследования Предварительный исследователь экспорта](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Установите параметры внутри этой вкладки:

   1. Укажите относительный временной промежуток для просмотра. Если вы довольны существующим представлением, оставьте это в качестве **существующего таймфрейма.**
   
   1. Выберите между **агрегированными** и **сырыми событиями.** 
   
       > [!NOTE]
       > Вы всегда можете агрегировать данные позже в Power BI, но вы не можете вернуться к необработанным данным после агрегирования. 
       
       > [!NOTE]
       > Для данных уровня Raw Event существует лимит отсчета 100-K событий.

       [![Подключения](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Если вы не настроили экземпляр Time Series Insights для **Теплого магазина,** вы получите предупреждение.

       [![Теплое предупреждение магазина](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Вы можете настроить существующий экземпляр для **теплого магазина** на портале Azure.

1. Выберите **запрос копирования для буфера обмена**.
1. Теперь запустите Power BI Desktop.
1. В Power BI Desktop на вкладке **Home** выберите **Get Data** в левом верхнем углу, а затем **подробнее.**

    [![Главная выпадение](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Поиск **по версии Time Series Insights**, выберите **Azure Time Series Insights (бета),** затем **подключитесь.**

    [![Подключите Power BI к анализу временных рядов](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Кроме того, перейдите на вкладку **Azure,** выберите **Azure Time Series Insights (бета),** затем **подключите**.
    
1. В поле диалога сообщений будет отображаться запрос на разрешение на подключение к сторонним ресурсам. Выберите **продолжить**.

    [![Выберите Создать пользовательский запрос](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. В меню выпадающих **данных**под source data выберите **пользовательский запрос.** Вставьте из буфера обмена в дополнительное **пользовательское запрос (необязательно)** поле ниже, а затем нажмите **OK**.

    [![Перейдите в пользовательском запросе и выберите OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. Теперь таблица данных будет загружена. Нажмите **нагрузку** для загрузки в Power BI.

    [![Просмотрите загруженные данные в таблице и выберите Load](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Если вы выполнили эти шаги, перейдите к следующему разделу.

## <a name="create-a-report-with-visuals"></a>Создание отчета с визуальными элементами

Теперь, когда вы импортировали данные в Power BI, пришло время создать отчет с визуальными эффектами.

1. В левой части окна убедитесь, что вы выбрали представление **отчета.**

    [![Выберите представление отчета](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  В колонке **Визуализации** выберите визуальный выбор. Например, выберите **диаграмму строки.** Это добавит диаграмму пустой линии к вашему холсту.
 
1.  В списке **Полей** выберите **Timestamp** и перетащите ее на поле **Axis** для отображения элементов вдоль X-оси.

1.  Опять же, в списке **Полей** выберите **TimeSeriesId** и перетащите его в поле **Значений** для отображения элементов по оси Y.

    [![Создание графика](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Чтобы добавить еще одну диаграмму в холст, выберите место на холсте за пределами диаграммы строки и повторите этот процесс.

    [![Создание дополнительных диаграмм для совместного участия](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

После создания отчета вы можете опубликовать его в службах отчетности Power BI.

## <a name="advanced-editing"></a>Расширенное редактирование

Если вы уже загрузили набор данных в Power BI, но хотели бы изменить запрос (например, параметры даты/времени или идентификатора среды), вы можете сделать это с помощью функциональности расширенного редактора Power BI. Обратитесь к [документации Power BI,](https://docs.microsoft.com/power-bi/desktop-query-overview) чтобы узнать больше.

В качестве обзора:

1. В Power BI Desktop выберите **Запросы на редактор.**
1. Пресс **Расширенный редактор**.

    [![Редит-запросы в расширенном редакторе](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Изменяем полезную нагрузку JSON по желанию.
1. Выберите **готовое,** а затем **закрыть & применить** в окно **редактора запроса питания**.

Интерфейс теперь будет отражать желаемые изменения, которые вы применили.  

## <a name="next-steps"></a>Next Steps

* Прочитайте о [концепциях разъема Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview) для исследования серии времени Azure.

* Узнайте больше о [рабочем столе Power BI.](https://docs.microsoft.com/power-bi/desktop-query-overview)

* Читайте [Время Серии Исследования GA Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) и Время [серии Исследования Предварительный исследователь](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).
