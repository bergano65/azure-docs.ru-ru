---
title: Как добавить наборы справочных данных в среду - Azure Time Series Исследования Документы Майкрософт
description: Из этой статьи вы узнаете, как добавить эталонный набор данных в среду аналитики временных рядов Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18~~~~
ms.openlocfilehash: 79628ed44753577023464ef6208027e1b7996d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087249"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Создание эталонного набора данных для среды Аналитики временных рядов с помощью портала Azure

Из этой статьи вы узнаете, как добавить эталонный набор данных в среду Аналитики временных рядов Azure. Соединив эталонные данные с исходными, можно получить расширенные значения.

Эталонный набор данных — это коллекция элементов, которые дополняют события из вашего источника событий. Обработчик входящего трафика аналитики временных рядов соединяет каждое событие из вашего источника событий с соответствующей строкой данных в вашем эталонном наборе данных. Это дополненное событие становится доступным для запроса. Это соединение основано на столбцах первичного ключа, определенных в эталонном наборе данных.

Ретроактивное объединение с эталонными данными не поддерживается. Таким образом, только текущие и будущие данные о входе сопоставляются и присоединяются к набору эталонных дат после того, как они были настроены и загружены.

## <a name="video"></a>Видеоролик

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Узнайте о модели справочных данных Time Series Insight.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Добавление эталонного набора данных

1. Войдите на [портал Azure](https://portal.azure.com).

1. Найдите существующую среду исследования временных серий Azure. На портале Azure щелкните **Все ресурсы** в меню слева. Выберите среду Time Series Insights.

1. Выберите страницу **Обзор**. Расширьте раздел Essentials в верхней части **страницы,** чтобы найти **URL-адрес Explorer Time Series Insights** и открыть ссылку.  

   [![Расширить раздел Essentials](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   Просмотр исследователя для среды Исследования временных рядов.

1. Расширьте селектор среды в Explorer Time Series Insights. Выберите активную среду. Выберите значок эталонных данных в правом верхнем углу на странице обозревателя.

   [![Добавление эталонных данных](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Нажмите кнопку **+ Добавить набор данных**, чтобы начать добавление нового набора данных.

   [![Добавление набора данных](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. На странице **Новый эталонный набор данных** выберите формат данных:

   - выберите **CSV-файл** для данных с разделителями-запятыми (первая строка считается строкой заголовка);
   - Выберите **JSON Array** для отформатированных данных объектов JavaScript (JSON).

   [![Выберите формат данных.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. Укажите данные, используя один из двух способов:

   - вставьте данные в текстовый редактор а затем нажмите кнопку **Проанализировать эталонные данные**;
   - нажмите кнопку **Выберите файл**, чтобы добавить данные из локального текстового файла.

   Например, вставьте данные CSV: [ ![Вставленные данные CSV](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Например, вставьте данные массива JSON: [ ![Вставьте данные JSON](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   Если при анализе значений данных произойдет ошибка, она отобразится красным цветом в нижней части страницы, например таким образом: `CSV parsing error, no rows extracted`.

1. После успешного анализа данных появится сетка данных с отображением столбцов и строк с данными. Просмотрите сетку данных, чтобы убедиться, что все правильно.

   [![Обзор справочных данных](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Просмотрите каждый столбец, чтобы понять предполагаемый тип данных, и при необходимости измените тип данных.  Выберите символ типа данных **#** в заголовке столбца: для двойных (числовых данных), **ТЗ F** для boolean, или **Abc** для шнура.

   [![Выбор типов данных в заголовках столбцов](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. При необходимости переименуйте заголовки столбцов. Имя ключевого столбца необходимо для присоединения к соответствующему свойству в источнике события. 

   > [!IMPORTANT]
   > Убедитесь, что имя ключевого столбца эталонных данных точно совпадает с именем события для входящих данных, включая чувствительность к регистру. Имена столбцов, не являющихся ключевыми, используются для добавления входящих данных с соответствующими значениями эталонных данных.

1. Введите значение в поле **Filter the rows...** (Отфильтровать строки...), чтобы при необходимости просмотреть отдельные строки. Фильтр используется для просмотра данных, но не применяется при их загрузке.

1. Присвойте имя набору данных, заполнив поле **Имя набора данных** над сеткой данных.

    [![Присвоение имени набору данных](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Укажите столбец **первичного ключа** в наборе данных, выбрав раскрывающееся меню над сеткой данных.

    [![Выбор столбца первичного ключа](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Необязательно)** Выберите **+** кнопку, чтобы добавить вторичный столбец ключа, как составной основной ключ. Если нужно отменить выбор, выберите пустое значение из раскрывающегося списка, чтобы удалить вторичный ключ.

1. Для передачи данных нажмите кнопку **передачи строк**.

    [![Загружайте строки и подтверждайте данные.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    На странице вы увидите подтверждение завершенной загрузки и сообщение **Набор данных успешно отправлен**.

    > [!WARNING]
    > Столбцы или свойства, общие между наборами справочных данных, будут отображать ошибку загрузки **двойного имени свойства.** Ошибка не помешает успешной загрузке наборов справочных данных. Он может быть удален путем объединения строк, разделяющих дублированное имя свойства.

1. Выберите **Добавить строку,** **массовые строки импорта**или **добавить столбец,** чтобы добавить больше значений справочных данных, по мере необходимости.

    [![Добавьте строку, строки импорта намассовых или добавьте столбец.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Любой ряд, который разделяет уникальный ключ с другой строкой будет иметь свои столбцы переопределяется последней строки добавил, что акции, что уникальный ключ.

   > [!NOTE]
   > Добавленные строки **не** должны быть *прямоугольными* - они могут иметь меньше, больше или различные столбцы от других записей в наборе справочных данных.

## <a name="next-steps"></a>Дальнейшие действия

* [Управление эталонными данными](time-series-insights-manage-reference-data-csharp.md) программными средствами.

* Для полной ссылки API прочитайте документ [API справочных данных.](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api)
