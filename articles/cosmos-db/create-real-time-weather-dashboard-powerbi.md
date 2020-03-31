---
title: Создание панели мониторинга в реальном времени с помощью Azure Cosmos DB, служб анализа Azure и Power BI
description: Узнайте, как создать панель мониторинга погоды в Power BI с помощью служб анализа Azure Cosmos и Azure Analysis Services.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70376597"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Создание приборной панели в реальном времени с использованием Azure Cosmos DB и Power BI

В этой статье описаны шаги, необходимые для создания панели мониторинга погоды в Power BI с использованием Служб анализа Azure Cosmos и Azure Analysis Services. На приборной панели Power BI будут отображаться диаграммы, отображаемые в режиме реального времени информацию о температуре и количестве осадков в регионе.

## <a name="reporting-scenarios"></a>Сценарии отчетности

Существует несколько способов настройки панелей мониторинга отчетов на данных, хранящихся в Azure Cosmos DB. В зависимости от требований к застоям и размеру данных в следующей таблице описывается настройка отчетности для каждого сценария:


|Сценарий |Настройка |
|---------|---------|
|1. Создание специальных отчетов (без обновления)    |  [Разъем Power BI Azure Cosmos DB с режимом импорта](powerbi-visualize.md)       |
|2. Создание специальных отчетов с периодическим обновлением   |  [Разъем Power BI Azure Cosmos DB с режимом импорта (запланированное периодическое обновление)](powerbi-visualize.md)       |
|3. Отчетность по большим наборам данных (< 10 ГБ)     |  Разъем DB Power BI Azure Cosmos с постепенным обновлением       |
|4. Отчетность в режиме реального времени на больших наборах данных    |  Разъем служб анализа Power BI Azure с прямым запросом и службами анализа Azure (разъем Azure Cosmos DB)       |
|5. Отчетность по живым данным с агрегатами     |  [Разъем Power BI Spark с прямым запросом - Azure Databricks - Разъем Cosmos DB Spark.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. Отчетность по живым данным с агрегатами на больших наборах данных   |  Разъем служб ы питания BI Azure Analysis Services с прямым запросом - службы анализа Azure - Azure Databricks - разъем Cosmos DB Spark.       |

Сценарии 1 и 2 можно легко настроить с помощью разъема Azure Cosmos DB Power BI. В этой статье описаны настройки сценариев 3 и 4.

### <a name="power-bi-with-incremental-refresh"></a>Мощность BI с постепенным обновлением

Power BI имеет режим, в котором можно настроить постепенное обновление. Этот режим устраняет необходимость создания и управления разделами аналитических служб Azure. Инкрементные обновления можно настроить для фильтрации только последних обновлений в больших наборах данных. Однако этот режим работает только с сервисом Power BI Premium, который имеет ограничение набора данных в 10 ГБ.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Разъем анализа power BI Azure - службы анализа Azure

Службы анализа Azure предоставляют полностью управляемую платформу в качестве службы, которая размещает модели данных корпоративного класса в облаке. Массивные наборы данных можно загрузить из DB Azure Cosmos в службы анализа Azure. Чтобы избежать постоянного запроса всего набора данных, наборы данных можно подразделить на разделы Azure Analysis Services, которые могут обновляться независимо на разных частотах.

## <a name="power-bi-incremental-refresh"></a>Мощность BI инкрементное обновление

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>В Лазурный космос DB властные данные о погоде

Настройка конвейера приема для загрузки [метеорологических данных](https://catalog.data.gov/dataset/local-weather-archive) в Azure Cosmos DB. Можно настроить задание [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md) для периодической загрузки последних метеорологических данных в DB Azure Cosmos с помощью http Source и Cosmos DB.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Подключение Power BI к Azure Cosmos DB

1. **Подключите учетную запись Azure Cosmos к Power BI** - Откройте рабочий стол Power BI и используйте разъем Azure Cosmos DB для выбора правильной базы данных и контейнера.

   ![Соединитель Power BI Azure Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **Нанастройка инкрементного обновления** - Следуйте шагам [поэтапного обновления со](/power-bi/service-premium-incremental-refresh) статьей Power BI для настройки инкрементного обновления для набора данных. Добавьте параметры **RangeStart** и **RangeEnd,** показанные на следующем скриншоте:

   ![Параметры диапазона настройки](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   Поскольку набор данных имеет столбец Дата, который находится в текстовой форме, параметры **RangeStart** и **RangeEnd** должны быть преобразованы, чтобы использовать следующий фильтр. В панели **Расширенного редактора** изменяйте запрос, добавляйте следующий текст для фильтрации строк на основе параметров RangeStart и RangeEnd:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   В зависимости от того, какой столбец и тип данных присутствует в исходном наборе данных, можно соответствующим образом изменить поля RangeStart и RangeEnd

   
   |Свойство  |Тип данных  |Filter  |
   |---------|---------|---------|
   |_ts     |   Числовой      |  «_ts» > Duration.TotalSeconds (RangeStart - #datetime (1970, 1, 1, 0, 0, 0)) и «_ts» < Duration.TotalSeconds (RangeEnd - #datetime (1970, 1, 1, 0, 0, 0)))       |
   |Дата (например:- 2019-08-19)     |   Строка      | «Документ.дата» > DateTime.ToText (RangeStart,«yyyy-MM-dd») и «Документ.дата» < DateTime.ToText (RangeEnd,«yyyy-MM-dd»)        |
   |Дата (например:- 2019-08-11 12:00:   |  Строка       |  «Document.date»> DateTime.ToText (RangeStart, yyyy-mm-dd HH:mm:ss) и «Document.date» < DateTime.ToText (RangeEnd,«yyy-mm-dd HH:mm:ss»)       |


1. **Определите политику обновления** - Определите политику обновления, перенанося на вкладку **Incremental refresh** в **контекстном** меню для таблицы. Установите политику обновления, чтобы обновлять **каждый день** и хранить данные за последний месяц.

   ![Определение политики обновления](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   Игнорировать предупреждение, в которое говорится, что *запрос M не может быть подтвержден для сложенного.* Разъем Azure Cosmos DB складывает запросы фильтра.

1. **Загрузите данные и создайте отчеты** - используя данные, которые вы загрузили ранее, создайте диаграммы, чтобы сообщить о температуре и количестве осадков.

   ![Загрузка данных и генерация отчета](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **Опубликовать отчет power BI premium** - Поскольку постепенное обновление является только функцией Premium, диалог публикации позволяет только выбор рабочего пространства на емкости Premium. Первое обновление может занять много времени, так как данные импортируются за весь период. Последующие обновления данных намного быстрее, поскольку они используют постепенное обновление.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Разъем анализа power BI Azure - службы анализа Azure 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>В Лазурный космос DB властные данные о погоде 

Настройка конвейера приема для загрузки [метеорологических данных](https://catalog.data.gov/dataset/local-weather-archive) в Azure Cosmos DB. Можно настроить задание Azure Data Factory (ADF) для периодической загрузки последних метеорологических данных в DB Azure Cosmos с помощью HTTP Source и Cosmos DB Sink.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Подключение служб анализа Azure к учетной записи Azure Cosmos

1. **Создание нового кластера** - аналитических служб Azure[Создайте экземпляр служб анализа Azure](../analysis-services/analysis-services-create-server.md) в том же регионе, что и учетная запись Azure Cosmos и кластер Databricks.

1. **Создайте новый табулярный проект Аналитических служб в визуальной студии** -  [Установить инструменты для данных серверов S'L (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) и создать табулярный проект Аналитических служб в Visual Studio.

   ![Создание проекта аналитических служб Azure](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   Выберите **экземпляр Интегрированного рабочего пространства** и установленный уровень совместимости с **сервером S'L 2017 / Службы анализа Azure (1400)**

   ![Разработчик табликовых моделей Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **Добавьте источник данных Azure Cosmos DB** - Перейдите к **моделям**> **источников** > **данных нового источника данных** и добавьте источник данных Azure Cosmos DB, показанный на следующем скриншоте:

   ![Добавление источника данных Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   Подключитесь к Azure Cosmos DB, предоставив **учетную запись URI,** **имя базы данных**и **имя контейнера.** Теперь вы можете видеть данные из контейнера Azure Cosmos, импортируемые в Power BI.

   ![Предварительный просмотр данных Azure Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **Постройте модель аналитических служб** - Откройте редактор запроса, выполните необходимые операции для оптимизации загруженного набора данных:

   * Извлекайте только столбцы, связанные с погодой (температура и количество осадков)

   * Извлеките информацию за месяц из таблицы. Эти данные полезны при создании разделов, описанных в следующем разделе.

   * Преобразование температурных столбцов в число

   В результате M выражение заключается в следующем:

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

   Кроме того, измените тип данных температурных столбцов на десятичную, чтобы убедиться, что эти значения могут быть построены в Power BI.

1. **Создание разделов azure Analysis** - Создание разделов в службах анализа Azure для разделения набора данных на логические разделы, которые могут обновляться независимо и на разных частотах. В этом примере можно создать два раздела, которые разделят набор данных на данные за последний месяц и все остальное.

   ![Создание разделов аналитических служб](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   Создайте следующие два раздела в службах анализа Azure:

   * **Последний месяц** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Исторические** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Развертывание модели на сервере анализа Azure** - Нажмите на проект Azure Analysis Services и выберите **Развертывание.** Добавьте имя сервера в панель **свойств развертывания сервера.**

   ![Развертывание модели аналитических служб Azure](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **Настройка раздела обновляет и сливается** - Службы анализа Azure позволяют независимую обработку разделов. Поскольку мы хотим, чтобы раздел **«Последний месяц»** постоянно обновлялся с самыми последними данными, установите интервал обновления до 5 минут. Обновление данных в историческом разделе не требуется. Кроме того, необходимо написать код для консолидации раздела последнего месяца в исторический раздел и создания нового раздела последнего месяца.


## <a name="connect-power-bi-to-analysis-services"></a>Подключение Power BI к аналитическим службам

1. **Подключитесь к серверу анализа Azure с помощью системы анализа Azure Connector** - Выберите **режим Live** и подключитесь к экземпляру служб анализа Azure, как показано на следующем скриншоте:

   ![Получение данных из служб Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **Загрузите данные и создавайте отчеты** - используя данные, которые вы загрузили ранее, создайте диаграммы, чтобы сообщить о температуре и количестве осадков. Поскольку вы создаете живое соединение, запросы должны выполняться на данных в модели аналитических служб Azure, развернутых на предыдущем этапе. Температурные диаграммы будут обновлены в течение пяти минут после загрузки новых данных в Azure Cosmos DB.

   ![Загрузка данных и генерация отчетов](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о Power BI см. в статье [Приступая к работе с Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Подключение Qlik Sense к Azure Cosmos DB и визуализация данных](visualize-qlik-sense.md)