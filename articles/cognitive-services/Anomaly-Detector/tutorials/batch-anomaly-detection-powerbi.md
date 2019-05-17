---
title: Визуализировать аномалии, с помощью пакетной службы обнаружения и Power BI
titlesuffix: Azure Cognitive Services
description: Используйте API обнаружения аномалий и Power BI для визуализации аномалии в данных временных рядов.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 04/30/2019
ms.author: aahi
ms.openlocfilehash: 48257782bd71f6145adc7107a5fe70b4cc4f8305
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790476"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Руководство по Визуализировать аномалии, с помощью пакетной службы обнаружения и Power BI

Используйте этот учебник, чтобы найти аномалии в набор данных временного ряда в пакетном режиме. Power BI desktop будет использовать файл Excel, Подготовка данных для API обнаружения аномалий и визуализировать статистические аномалии на протяжении всего его.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Использовать Power BI Desktop для импорта и преобразования в набор данных временного ряда
> * Интеграция Power BI Desktop с помощью API обнаружения аномалий для обнаружения аномалий пакетной службы
> * Визуализируйте аномалии в данных, включая ожидаемое и просмотр значения и границы обнаружения аномалий.

## <a name="prerequisites"></a>Технические условия

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), которые доступны бесплатно.
* Указывает содержащий временного ряда excel (.xlsx) файла данных. Данные для примера в этом кратком руководстве можно найти на [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Загружать и форматировать данные временных рядов

Чтобы приступить к работе, откройте Power BI Desktop и загрузить данные временных рядов, загруженный из необходимых компонентов. Этот файл excel содержит ряд пар метку времени и значение времени (UTC).  

> [!NOTE]
> Power BI можно использовать данные из разнообразных источников, таких как CSV-файлы, SQL базы данных, хранилище больших двоичных объектов и многое другое.  

В главном окне Power BI Desktop щелкните **Главная** ленты. В **внешних данных** группу на ленте, откройте **получить данные** раскрывающегося меню и выберите пункт **Excel**.

![Изображение кнопки «Получить данные» в Power BI](../media/tutorials/power-bi-get-data-button.png)

После появления диалогового окна, перейдите к папке, куда вы загрузили пример XLSX-файла и выберите его. После **Навигатор** появляется диалоговое окно, нажмите кнопку **Sheet1**, а затем **изменить**.

![Изображение экрана «Навигатор» источника данных в Power BI](../media/tutorials/navigator-dialog-box.png)

Power BI преобразует отметки времени в первом столбце, чтобы `Date/Time` тип данных. Эти метки времени должны быть преобразованы в текст для отправки в API обнаружения аномалий. Если редактор Power Query не открывается автоматически, нажмите кнопку **изменить запросы** на вкладке "Главная". 

Нажмите кнопку **преобразования** ленты в редакторе Power Query. В **любой столбец** группы, откройте **тип данных:** раскрывающееся меню и выберите **текст**.

![Изображение экрана «Навигатор» источника данных в Power BI](../media/tutorials/data-type-drop-down.png)

Когда вы получите уведомление об изменении типа столбца, щелкните **замените текущий**. После этого щелкните **закрыть и применить** или **применить** в **Главная** ленты. 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Создание функции для отправки данных и формат ответа

Чтобы отформатировать и отправьте файл данных в API обнаружения аномалий, можно вызвать запроса таблицы, созданной ранее. В редакторе запросов Power из **Главная** ленты, откройте **новый источник** раскрывающегося меню и выберите пункт **пустой запрос**.

Убедитесь, что выбран новый запрос, а затем щелкните **расширенный редактор**. 

![Изображение кнопки «Расширенный редактор» в Power BI](../media/tutorials/advanced-editor-screen.png)

В расширенном редакторе используйте следующий фрагмент Power Query M для извлечения из таблицы столбцы и отправлять их в API. После этого запрос будет создать таблицу из ответа JSON и вернуть его. Замените `apiKey` переменной допустимый ключ API обнаружения аномалий, и `endpoint` с конечной точкой. После ввода запроса в расширенном редакторе щелкните **сделать**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect,
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({
                    
                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),
    
    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

Вызвать запрос на листе для данных, выбрав `Sheet1` ниже **введите параметр**и нажмите кнопку **Invoke**. 

![Изображение кнопки «Расширенный редактор»](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Конфиденциальность данных источника и проверки подлинности

> [!NOTE]
> Имейте в виду политик вашей организации для обеспечения конфиденциальности данных и доступа. См. в разделе [уровни конфиденциальности Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-privacy-levels) Дополнительные сведения.

Может появиться предупреждающее сообщение при попытке выполнить запрос, так как он использует внешний источник данных. 

![Рисунок, демонстрирующий предупреждения, созданные в Power BI](../media/tutorials/blocked-function.png)

Чтобы устранить эту проблему, щелкните **файл**, и **параметры и настройки**. Нажмите кнопку **параметры**. Ниже **текущего файла**выберите **конфиденциальности**, и **игнорировать уровни конфиденциальности и возможного улучшения производительности**. 

Кроме того может появиться сообщение с предложением указать способ подключения к API.

![Рисунок, демонстрирующий запрос, указав учетные данные для доступа](../media/tutorials/edit-credentials-message.png)

Чтобы устранить эту проблему, щелкните **изменить учетные данные** в сообщении. Когда появляется диалоговое окно, выберите **Anonymous** анонимное подключение к API. Щелкните **Подключить**. 

После этого щелкните **закрыть и применить** в **Главная** ленты, чтобы применить изменения.

## <a name="visualize-the-anomaly-detector-api-response"></a>Визуализация в ответе API обнаружения аномалий

В главном окне Power BI начинается с помощью запросов, созданной ранее для визуализации данных. Сначала выберите **график** в **визуализации**. Затем добавьте метку времени из вызванной функции график **оси**. Его правой кнопкой мыши и выберите **Timestamp**. 

![Щелкните правой кнопкой мыши значение метки времени](../media/tutorials/timestamp-right-click.png)

Добавьте следующие поля из **вызывается функция** в диаграмму **значения** поля. Используйте приведенном ниже снимке экрана для создания диаграммы.

    * Value
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![Изображение экрана новые быстрые меры](../media/tutorials/chart-settings.png)

После добавления поля, щелкните диаграмму и измените ее, чтобы показать все точки данных. Диаграмма будет выглядеть снимке экрана ниже:

![Изображение экрана новые быстрые меры](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Отображаются точки данных аномалий

В правой части окна Power BI ниже **поля** панели, щелкните правой кнопкой мыши **значение** под **запроса вызывается функция**и нажмите кнопку **новый быстрого мера**.

![Изображение экрана новые быстрые меры](../media/tutorials/new-quick-measure.png)

На появившемся экране выберите **отфильтрованные значение** вычисления. Задайте **базовое значение** для `Sum of Value`. Затем перетащите `IsAnomaly` из **вызывается функция** поля **фильтра**. Выберите `True` из **фильтра** раскрывающееся меню.

![Изображение экрана новые быстрые меры](../media/tutorials/new-quick-measure-2.png)

После нажатия кнопки **ОК**, у вас будет `Value for True` поле в нижней части списка полей. Щелкните его правой кнопкой мыши и переименуйте его в **аномалий**. Добавление диаграммы **значения**. Затем выберите **формат** инструмент, а также задать тип оси x **Категориальный**.

![Изображение экрана новые быстрые меры](../media/tutorials/format-x-axis.png)

Цвета диаграммы, щелкнув **формат** средство и **цвета данных**. Диаграмма должна выглядеть примерно следующим образом:

![Изображение экрана новые быстрые меры](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
>[Потоковая передача обнаружение аномалий с помощью Azure Databricks](anomaly-detection-streaming-databricks.md)
