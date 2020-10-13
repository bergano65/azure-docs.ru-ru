---
title: Power BI и синапсе SQL Server для анализа Azure Cosmos DB данных с помощью ссылки синапсе
description: Узнайте, как создать синапсе базу данных без SQL Server и представления по ссылке синапсе для Azure Cosmos DB, запросить контейнеры Cosmos для Azure, а затем создать модель с Power BI для этих представлений.
author: ArnoMicrosoft
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/22/2020
ms.author: acomet
ms.openlocfilehash: 03ea1b0cdfef30935b38078d0811d1408a78c41e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937982"
---
# <a name="use-power-bi-and-synapse-sql-serverless-to-analyze-azure-cosmos-db-data-with-synapse-link-preview"></a>Использование Power BI и синапсе SQL Server для анализа Azure Cosmos DB данных с помощью ссылки синапсе (Предварительная версия)

В этой статье вы узнаете, как создать синапсе SQL Server (который ранее назывался "база данных **SQL по запросу**") и представлениями по ссылке синапсе для Azure Cosmos DB. Вы запрашиваете контейнеры Azure Cosmos, а затем создаете модель с Power BI для этих представлений, чтобы отразить этот запрос.

> [!NOTE]
> Использование аналитического хранилища Azure Cosmos DB с синапсе SQL Server в настоящее время находится на этапе предварительной версии. Чтобы запросить доступ, обратитесь к [группе Azure Cosmos DB](mailto:cosmosdbsynapselink@microsoft.com).

В этом сценарии вы будете использовать фиктивные данные о продажах продуктов в розничном магазине партнера. Вы проанализируете доход на каждый магазин, основываясь на большом семейе и влиянии рекламы на конкретную неделю. В этой статье вы создадите два представления с именами **ретаилсалес** и **сторедемографикс** и запрос между ними. Образец данных по продукту можно получить из репозитория [GitHub](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData) .

## <a name="prerequisites"></a>Предварительные требования

Прежде чем начать, обязательно создайте следующие ресурсы:

* [Создайте учетную запись Azure Cosmos DB типа SQL (Core) или MongoDB.](create-cosmosdb-resources-portal.md)

* Включение ссылки Azure синапсе для вашей [учетной записи Azure Cosmos](configure-synapse-link.md#enable-synapse-link)

* Создайте базу данных в учетной записи Azure Cosmos и два контейнера с [включенным аналитическим хранилищем.](configure-synapse-link.md#create-analytical-ttl)

* Загрузите данные продуктов в контейнеры Azure Cosmos, как описано в записной книжке [приема данных пакетной](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb) службы.

* [Создайте рабочую область синапсе](../synapse-analytics/quickstart-create-workspace.md) с именем **синапселинкби**.

* [Подключите базу данных Azure Cosmos к рабочей области синапсе](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json).

## <a name="create-a-database-and-views"></a>Создание базы данных и представлений

В рабочей области синапсе перейдите на вкладку **Разработка** , щелкните **+** значок и выберите **скрипт SQL**.

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="Добавление скрипта SQL в рабочую область синапсе Analytics":::

Каждая Рабочая область поставляется с конечной точкой синапсе SQL Server без сервера. После создания скрипта SQL на панели инструментов в верхней части окна подключитесь к **SQL по запросу**.

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="Добавление скрипта SQL в рабочую область синапсе Analytics":::

Создайте новую базу данных с именем **ретаилкосмосдб**и представление SQL для контейнеров с включенной ссылкой синапсе. Следующая команда показывает, как создать базу данных.

```sql
-- Create database
Create database RetailCosmosDB
```

Затем создайте несколько представлений в разных контейнерах Azure Cosmos с поддержкой канала синапсе. Это позволит использовать T-SQL для объединения и запроса Azure Cosmos DB данных, размещенных в разных контейнерах.  При создании представлений обязательно выберите базу данных **ретаилкосмосдб** .

В следующих скриптах показано, как создавать представления для каждого контейнера. Для простоты давайте воспользуемся функцией [автоматического вывода схемы](analytical-store-introduction.md#analytical-schema) в синапсе контейнерах с поддержкой ссылок СИНАПСЕ в SQL Server.


### <a name="retailsales-view"></a>Ретаилсалес представление:

```sql
-- Create view for RetailSales container
CREATE VIEW  RetailSales
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>',RetailSales)
AS q1
```

Обязательно вставьте Azure Cosmos DB регион и первичный ключ в предыдущий скрипт SQL. Все символы в имени региона должны быть в нижнем регистре без пробелов. В отличие от других параметров `OPENROWSET` команды, параметр имени контейнера должен быть указан без кавычек.

### <a name="storedemographics-view"></a>Сторедемографикс представление:

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

Теперь запустите скрипт SQL, выбрав команду **выполнить** .

## <a name="query-the-views"></a>Запрос представлений

Теперь, когда создаются два представления, определим запрос для объединения этих двух представлений следующим образом:

```sql
SELECT 
sum(p.[revenue]) as revenue
,p.[advertising]
,p.[storeId]
,p.[weekStarting]
,q.[largeHH]
 FROM [dbo].[RetailSales] as p
INNER JOIN [dbo].[StoreDemographics] as q ON q.[storeId] = p.[storeId]
GROUP BY p.[advertising], p.[storeId], p.[weekStarting], q.[largeHH]
```

Выберите **Запуск** , который предоставляет следующую таблицу в качестве результата:

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="Добавление скрипта SQL в рабочую область синапсе Analytics":::

## <a name="model-views-over-containers-with-power-bi"></a>Представления модели над контейнерами с Power BI

Затем откройте рабочий стол Power BI и подключитесь к конечной точке синапсе SQL Server, выполнив следующие действия.

1. Откройте приложение Power BI Desktop. Выберите **получить данные** и нажмите кнопку **больше**.

1. Выберите **Azure синапсе Analytics (SQL DW)** в списке параметров подключения.

1. Введите имя конечной точки SQL, в которой находится база данных. Введите `SynapseLinkBI-ondemand.sql.azuresynapse.net` в поле **сервер** . В этом примере  **синапселинкби** — имя рабочей области. Замените его, если вы получили другое имя для вашей рабочей области. Выберите **прямой запрос** для режима подключения к данным, а затем нажмите **кнопку ОК**.

1. Выберите предпочтительный метод проверки подлинности, например Azure AD.

1. Выберите базу данных **ретаилкосмосдб** и представления **ретаилсалес**, **сторедемографикс** .

1. Выберите **загрузить** , чтобы загрузить два представления в режиме прямого запроса.

1. Выберите **модель** , чтобы создать связь между двумя представлениями через столбец **storeId** .

1. Перетащите столбец **StoreId** из представления **ретаилсалес** в сторону столбца **StoreId** в представлении **сторедемографикс** .

1. Выберите отношение "многие к одному" (*: 1), так как существует несколько строк с одним и тем же ИДЕНТИФИКАТОРом в представлении **ретаилсалес** , но у **сторедемографикс** есть только одна строка идентификатора хранилища (это таблица измерения).

Теперь перейдите в окно **отчета** и создайте отчет для сравнения относительной важности размера семьи с средним доходом по магазину на основе дискретного представления дохода и индекса ларжехх:

1. Выберите **точечная диаграмма**.

1. Перетащите **ларжехх** из представления **Сторедемографикс** в ось X.

1. Перетащите **доход** из представления **Ретаилсалес** в ось Y. Выберите **Среднее** , чтобы получить среднее количество продаж на каждый магазин и за неделю.

1. Перетащите элемент **productCode** из представления **ретаилсалес** в условные обозначения, чтобы выбрать определенную линейку продуктов.
После выбора этих параметров вы увидите диаграмму, как на следующем снимке экрана:

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="Добавление скрипта SQL в рабочую область синапсе Analytics":::

## <a name="next-steps"></a>Дальнейшие шаги

Использование синапсе SQL Server для [анализа открытых наборов данных Azure и визуализация результатов в Azure синапсе Studio](../synapse-analytics/sql/tutorial-data-analyst.md)
