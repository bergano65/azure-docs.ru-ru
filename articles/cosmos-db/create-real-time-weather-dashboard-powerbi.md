---
title: Создание панели мониторинга в режиме реального времени с помощью Azure Cosmos DB, Azure Analysis Services и Power BI
description: Узнайте, как создать динамическую панель мониторинга погоды в Power BI с помощью Azure Cosmos DB и Azure Analysis Services.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376597"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Создание панели мониторинга в режиме реального времени с помощью Azure Cosmos DB и Power BI

В этой статье описываются шаги, необходимые для создания панели мониторинга "динамический Погода" в Power BI с помощью Azure Cosmos DB и Azure Analysis Services. На панели мониторинга Power BI отобразятся диаграммы для отображения сведений о температуре и лавины в регионе в режиме реального времени.

## <a name="reporting-scenarios"></a>Сценарии создания отчетов

Существует несколько способов настройки панелей мониторинга отчетов для данных, хранящихся в Azure Cosmos DB. В зависимости от требований устаревания и размера данных в следующей таблице описывается настройка отчетов для каждого сценария.


|Сценарий |Установка |
|---------|---------|
|1. Создание специальных отчетов (без обновления)    |  [Power BI Azure Cosmos DB соединителя с режимом импорта](powerbi-visualize.md)       |
|2. Создание специальных отчетов с периодическим обновлением   |  [Power BI Azure Cosmos DB соединителя с режимом импорта (запланированное периодическое обновление)](powerbi-visualize.md)       |
|3. Отчетность по большим наборам данных (< 10 ГБ)     |  Соединитель Azure Cosmos DB Power BI с добавочным обновлением       |
|4. Создание отчетов в режиме реального времени в больших наборах данных    |  Соединитель Azure Analysis Services Power BI с прямым запросом + Azure Analysis Services (соединитель Azure Cosmos DB)       |
|5. Создание отчетов о динамических данных с помощью статистических выражений     |  [Соединитель Spark Power BI с прямым запросом + Azure Databricks + Cosmos DB с помощью соединителя Spark.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. Создание отчетов о динамических данных с помощью статистических выражений в больших наборах данных   |  Power BI Azure Analysis Services Connector с прямым запросом + Azure Analysis Services + Azure Databricks + Cosmos DB соединителя Spark.       |

Сценарии 1 и 2 можно легко настроить с помощью соединителя Azure Cosmos DB Power BI. В этой статье описываются настройки сценариев 3 и 4.

### <a name="power-bi-with-incremental-refresh"></a>Power BI с добавочным обновлением

Power BI имеет режим, в котором можно настроить добавочное обновление. Этот режим устраняет необходимость создавать секции Azure Analysis Services и управлять ими. Добавочное обновление можно настроить для фильтрации только последних обновлений в больших наборах данных. Однако этот режим работает только со службой Power BI Premium с ограничением набора данных 10 ГБ.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI соединителя Azure Analysis Azure Analysis Services

Azure Analysis Services предоставляет полностью управляемую платформу как службу, в которой размещаются модели данных корпоративного уровня в облаке. Большие наборы данных можно загрузить из Azure Cosmos DB в Azure Analysis Services. Чтобы избежать полного запроса ко всему набору данных, наборы данных можно разделить на Azure Analysis Services секции, которые можно обновлять независимо с различной частотой.

## <a name="power-bi-incremental-refresh"></a>Power BI добавочного обновления

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Прием данных о погоде в Azure Cosmos DB

Настройте конвейер приема для загрузки [данных погоды](https://catalog.data.gov/dataset/local-weather-archive) в Azure Cosmos DB. Вы можете настроить задание [фабрики данных Azure (ADF)](../data-factory/connector-azure-cosmos-db.md) для периодической загрузки последних данных о погоде в Azure Cosmos DB с помощью источника HTTP и приемника Cosmos DB.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Подключение Power BI к Azure Cosmos DB

1. **Подключите учетную запись Azure Cosmos к Power BI** — откройте Power BI Desktop и с помощью соединителя Azure Cosmos DB выберите нужную базу данных и контейнер.

   ![Соединитель Power BI Azure Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **Настройка добавочного обновления** . Чтобы настроить добавочное обновление для набора данных, выполните действия, описанные в разделе [Добавочное обновление с помощью Power BIной](/power-bi/service-premium-incremental-refresh) статьи. Добавьте параметры **RangeStart** и **RangeEnd** , как показано на следующем снимке экрана:

   ![Настройка параметров диапазона](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   Так как набор данных содержит столбец дат в текстовом формате, параметры **RangeStart** и **RangeEnd** должны быть преобразованы для использования следующего фильтра. В области **Расширенный редактор** измените запрос, добавив следующий текст для фильтрации строк на основе параметров RangeStart и RangeEnd:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   В зависимости от того, какой столбец и тип данных есть в исходном наборе данных, можно соответствующим образом изменить поля RangeStart и RangeEnd

   
   |Свойство  |Тип данных  |Filter  |
   |---------|---------|---------|
   |_ts     |   Numeric      |  [_TS] > Duration. Тоталсекондс (RangeStart-#datetime (1970, 1, 1, 0, 0, 0)) и [_TS] < Duration. Тоталсекондс (RangeEnd-#datetime (1970, 1, 1, 0, 0, 0)))       |
   |Дата (например,-2019-08-19).     |   Строковое      | [Document. Date] > DateTime. Тотекст (RangeStart, "гггг-мм-дд") и [Document. Date] < DateTime. Тотекст (RangeEnd, "гггг-мм-дд")        |
   |Дата (например,-2019-08-11 12:00:00).   |  Строковое       |  [Document. Date] > DateTime. Тотекст (RangeStart, "гггг-мм-дд чч: мм: СС") и [Document. Date] < DateTime. Тотекст (RangeEnd, "гггг-мм-дд чч: мм: СС")       |


1. **Определение политики обновления** . Определите политику обновления, перейдя на вкладку **Добавочное обновление** **контекстного** меню для таблицы. Настройте политику обновления на **ежедневное** обновление и сохраните данные за прошлый месяц.

   ![Определение политики обновления](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   Игнорируйте предупреждение, сообщающее, что *запрос M не может быть подтвержден для свертывания*. Соединитель Azure Cosmos DB растрет запросы фильтра.

1. **Загрузите данные и создайте отчеты** — используя ранее загруженные данные, создайте диаграммы для создания отчетов о температуре и лавины.

   ![Загрузка данных и создание отчета](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **Публикация отчета в Power BI "Премиум** ". Поскольку добавочное обновление является только расширенным, диалоговое окно публикации позволяет выбрать рабочую область только для емкости Premium. В первом обновлении может потребоваться больше времени для импорта исторических данных. Последующие обновления данных выполняются гораздо быстрее, так как они используют добавочное обновление.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI соединителя Azure Analysis Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Прием данных о погоде в Azure Cosmos DB 

Настройте конвейер приема для загрузки [данных погоды](https://catalog.data.gov/dataset/local-weather-archive) в Azure Cosmos DB. Вы можете настроить задание фабрики данных Azure (ADF) для периодической загрузки последних данных о погоде в Azure Cosmos DB с помощью источника HTTP и приемника Cosmos DB.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Подключение Azure Analysis Services к учетной записи Azure Cosmos

1. **Создание нового кластера** - Azure Analysis Services[Создайте экземпляр служб Azure Analysis Services](../analysis-services/analysis-services-create-server.md) в том же регионе, что и учетная запись Azure Cosmos и кластер кирпичей.

1. **Создание нового Analysis Services табличного проекта в Visual Studio** -  [установите SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) и создайте Analysis Services табличный проект в Visual Studio.

   ![Создание проекта Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   Выберите экземпляр **интегрированной рабочей области** и установите уровень совместимости **SQL Server 2017/Azure Analysis Services (1400)** .

   ![Конструктор табличных моделей Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **Добавьте Azure Cosmos DB источник данных** — перейдите к разделу **модели**> **источники** > данных**новый источник данных** и добавьте Azure Cosmos DB источник данных, как показано на следующем снимке экрана:

   ![Добавить Cosmos DB источник данных](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   Подключитесь к Azure Cosmos DB, указав **URI учетной записи**, **имя базы данных**и **имя контейнера**. Теперь вы можете увидеть данные из контейнера Azure Cosmos, импортированные в Power BI.

   ![Предварительный просмотр Azure Cosmos DB данных](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **Создайте модель Analysis Services** — откройте редактор запросов, выполните необходимые операции для оптимизации загруженного набора данных.

   * Извлечение только столбцов, связанных с погодой (температура и лавины)

   * Извлеките сведения о месяце из таблицы. Эти данные полезны при создании секций, как описано в следующем разделе.

   * Преобразование столбцов температуры в число

   Полученное выражение M выглядит следующим образом:

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   Кроме того, измените тип данных столбца температура на Decimal, чтобы убедиться, что эти значения можно отобразить в Power BI.

1. **Создание секций анализа Azure** . Создание секций в Azure Analysis Services для разделения набора данных на логические разделы, которые могут обновляться независимо друг от друга и с разной частотой. В этом примере создаются две секции, которые делят набор данных на данные за последний месяц и все остальное.

   ![Создание секций служб Analysis Services](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   В Azure Analysis Services создайте следующие две секции:

   * **Последний месяц** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Исторических** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Разверните модель в сервер анализа данных Azure** . Щелкните правой кнопкой мыши проект Azure Analysis Services и выберите пункт **развернуть**. Добавьте имя сервера в области **Свойства сервера развертывания** .

   ![Развертывание модели Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **Настройка обновлений секций и слияний** — Azure Analysis Services позволяет независимой обработки секций. Так как мы хотим, чтобы Секция **последнего месяца** постоянно обновлялась с самыми последними данными, установите для интервала обновления значение 5 минут. Обновление данных в исторических разделах не требуется. Кроме того, необходимо написать код для консолидации секции последнего месяца с историческим разделом и создания новой секции последнего месяца.


## <a name="connect-power-bi-to-analysis-services"></a>Подключение Power BI к Analysis Services

1. **Подключитесь к сервер анализа данных Azure с помощью соединителя базы данных Azure Analysis Services** . Выберите **режим в режиме реального времени** и подключитесь к экземпляру Azure Analysis Services, как показано на следующем снимке экрана:

   ![Получение данных из служб Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **Загрузите данные и создайте отчеты** — используя ранее загруженные данные, создайте диаграммы для отчета о температуре и лавины. Так как вы создаете активное подключение, запросы должны выполняться для данных в модели Azure Analysis Services, которая была развернута на предыдущем шаге. Диаграммы температуры будут обновлены в течение пяти минут после загрузки новых данных в Azure Cosmos DB.

   ![Загрузка данных и создание отчетов](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о Power BI см. в статье [Приступая к работе с Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Подключение Qlik Sense для Azure Cosmos DB и визуализации данных](visualize-qlik-sense.md)