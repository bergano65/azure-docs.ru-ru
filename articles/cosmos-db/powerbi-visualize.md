---
title: Руководство по Power BI для соединителя Azure Cosmos DB
description: Используйте это руководство по Power BI для импорта JSON, создания информативных отчетов и визуализации данных с помощью соединителя Azure Cosmos DB и Power BI.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: fc3ca5fdde464ba63671512a6ebecd2c314cb192
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570831"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Визуализация данных Azure Cosmos DB с помощью соединителя Power BI

[Power BI](https://powerbi.microsoft.com/) — это веб-служба, в которой можно создавать и совместно использовать панели мониторинга и отчеты. Power BI Desktop — это средство разработки отчетов, позволяющее получать данные из различных источников данных. Azure Cosmos DB является одним из источника данных, который можно использовать с Power BI Desktop. Power BI Desktop можно подключить к учетной записи Azure Cosmos DB с помощью соединителя Azure Cosmos DB для Power BI.  После импорта в Power BI данные Azure Cosmos DB можно преобразовывать, создавать отчеты и публиковать их в Power BI.   

В этой статье описываются шаги, необходимые для подключения учетной записи Azure Cosmos DB к Power BI Desktop. После подключения перейдите к коллекции, извлеките данные, преобразуйте данные JSON в табличный формат и опубликуйте отчет в Power BI.

> [!NOTE]
> Соединитель Power BI для Azure Cosmos DB подключается к Power BI Desktop. Отчеты, созданные в Power BI Desktop, можно опубликовать на сайте PowerBI.com. Напрямую извлекать данные Azure Cosmos DB с PowerBI.com невозможно. 

> [!NOTE]
> Подключение к Azure Cosmos DB с помощью соединителя Power BI в настоящее время поддерживается только для учетных записей API SQL Azure Cosmos DB и Gremlin API.

## <a name="prerequisites"></a>Обязательные условия
Перед выполнением инструкций в этом руководстве по Power BI убедитесь в наличии доступа к следующим ресурсам:

* [загруженной последней версии Power BI Desktop](https://powerbi.microsoft.com/desktop);

* загруженному из GitHub [примеру данных о вулканах](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json);

* [Создайте учетную запись базы данных Azure Cosmos](https://azure.microsoft.com/documentation/articles/create-account/) и импортируйте данные о вулканах с помощью [средства переноса данных Azure Cosmos DB](import-data.md). При импорте данных советуем использовать в средстве переноса следующие параметры источника и целевых расположений:

   * **Параметры источника** 

       * **Импорт из:** JSON-файлы

   * **Параметры целевых расположений** 

      * **Строка подключения:**`AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Ключ секции**: /страна 

      * **Пропускная способность коллекции:** 1000 

Для совместного использования отчетов в службе PowerBI.com необходимо настроить учетную запись в PowerBI.com.  Дополнительные сведения о Power BI и Power BI Pro см. в разделе [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing) .

## <a name="lets-get-started"></a>Начало работы
Для работы с этим руководством представим, что вы геолог, изучающий вулканы по всему миру. Данные о вулканах хранятся в учетной записи Azure Cosmos DB, а формат документа JSON выглядит следующим образом.

```json
{
    "Volcano Name": "Rainier",
        "Country": "United States",
        "Region": "US-Washington",
        "Location": {
          "type": "Point",
          "coordinates": [
            -121.758,
            46.87
          ]
        },
        "Elevation": 4392,
        "Type": "Stratovolcano",
        "Status": "Dendrochronology",
        "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Вам нужно получить данные о вулканах из учетной записи Azure Cosmos DB и представить их в виде интерактивного отчета Power BI.

1. Запустите Power BI Desktop.

2. Непосредственно на экране приветствия можно **получить данные**, просмотреть **последние источники** или **открыть другие отчеты**. Щелкните значок X в правом верхнем углу, чтобы закрыть этот экран. Откроется представление **Отчет** средства Power BI Desktop.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI":::

3. Откройте ленту **Главная** и щелкните **Получить данные**.  Откроется окно **Получение данных** .

4. Выберите **Azure**, затем — **Azure Cosmos DB (бета-версия)** и щелкните **Подключить**. 

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbigetdata.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI":::

5. На странице **Предварительная версия соединителя** нажмите кнопку **Продолжить**. Откроется окно **Azure Cosmos DB**.

6. Укажите URL-адрес конечной точки учетной записи Azure Cosmos DB, из которой необходимо получить данные, как показано ниже, и нажмите кнопку **ОК**. Чтобы использовать собственную учетную запись, можно найти URL-адрес в поле универсального кода ресурса (URI) в колонке **Ключи** на портале Azure. При необходимости можно задать имя базы данных, имя коллекции или использовать навигатор для выбора базы данных и коллекции, чтобы определить источник данных.
   
7. При подключении к этой конечной точке в первый раз отображается запрос на введение ключа учетной записи. Для собственной учетной записи найдите ключ в поле **Первичный ключ** в колонке **Ключи только для чтения** на портале Azure. Введите соответствующий ключ и нажмите кнопку **Подключить**.
   
   При создании отчетов мы рекомендуем использовать ключ только для чтения. Это предотвращает нежелательное воздействие первичного ключа на потенциальные угрозы безопасности. Ключ только для чтения доступен в колонке **Ключи** на портале Azure. 
    
8. После успешного подключения учетной записи откроется панель **Навигатор**. На панели **Навигатор** отображается список баз данных в учетной записи.

9. Выберите и разверните узел базы данных, из которого поступают данные для отчета, а затем выберите **volcanodb** (имя базы данных может отличаться).   

10. Теперь выберите коллекцию, содержащую данные для извлечения, и выберите **volcano1** (имя коллекции может отличаться).
    
    В области «Предварительный просмотр» отображается список элементов **Запись** .  Документ представлен как тип **Запись** в Power BI. Точно так же вложенный блок JSON в документе является **Записью**.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbinavigator.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI":::

12. Нажмите кнопку **Изменить**, чтобы открыть в новом окне редактор запросов для преобразования данных.

## <a name="flattening-and-transforming-json-documents"></a>Преобразование документов JSON в плоскую структуру и их трансформация
1. Переключитесь в окно редактора запросов Power BI, где по центру отображается столбец **Документ**.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI" и "Last Know Eruption", а затем нажмите кнопку "ОK". Снимите флажок **Использовать исходное имя столбца как префикс** и щелкните **ОК**.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI":::

1. В центральной области отображается предварительный просмотр результата с выбранными полями.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI":::

1. В нашем примере свойство «Расположение» является блоком GeoJSON в документе.  Как видно, значение свойства «Расположение» представлено типом **Запись** в Power BI Desktop.  

1. Щелкните элемент развертывания справа от заголовка столбца Document.Location.  Отобразится контекстное меню с полями типа и координат.  Выберите здесь поле координат, снимите флажок **Использовать исходное имя столбца как префикс** и щелкните **ОК**.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI":::

1. Теперь в центральной области отображается столбец с координатами типа **Список** .  Как показано в начале этого руководства, данные GeoJSON в руководстве принадлежат типу «Точка» со значениями широты и долготы, записанными в массив координат.
   
   Элемент coordinates[0] представляет долготу, а элемент coordinates[1] — широту.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI":::

1. Чтобы преобразовать массив координат в плоскую структуру, создайте **настраиваемый столбец** с именем LatLong.  Выберите **Добавление столбца** на ленте и щелкните **Настраиваемый столбец**.  Откроется окно **Настраиваемый столбец**.

1. Укажите имя нового столбца, например LatLong.

1. Затем укажите настраиваемую формулу для нового столбца.  В нашем примере мы соединим значения широты и долготы, разделяя их запятыми, как показано ниже, используя следующую формулу: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Нажмите кнопку **ОК**.
   
   Дополнительные сведения о выражениях анализа данных (DAX), включая функции DAX, см. в статьях [основы DAX в Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-learn-dax-basics).
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI":::

1. Теперь в центральной области отображается новый столбец LatLong, заполненный значениями.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI" соответствуют указанным на следующем рисунке:
    
    :::image type="content" source="./media/powerbi-visualize/power-bi-applied-steps.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI":::
    
    Если ваши шаги отличаются, удалите лишние шаги и снова попробуйте добавить настраиваемый столбец. 

1. Чтобы сохранить модель данных, нажмите кнопку **Закрыть и применить** .

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicloseapply.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI":::

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Создание отчетов

Для создания отчетов, в которых наглядно представлены данные, используется представление отчета в Power BI Desktop.  Отчеты можно создавать путем перетаскивания полей на поле **Отчет** .

:::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview2.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI" щелкните **Сохранить** и сохраните файл с именем PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Публикация и совместное использование отчета
Для совместного использования отчета необходимо иметь учетную запись в службе PowerBI.com.

1. В средстве Power BI Desktop откройте ленту **Главная** .
1. Нажмите кнопку **Опубликовать**.  Вы увидите запрос на ввод имени пользователя и пароля учетной записи PowerBI.com.
1. После аутентификации учетных данных отчет публикуется в указанном вами расположении.
1. Для просмотра и совместного использования отчета на сайте PowerBI.com щелкните **Открыть PowerBITutorial.pbix в Power BI** .
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI":::

## <a name="create-a-dashboard-in-powerbicom"></a>Создание панели мониторинга на PowerBI.com
Теперь, когда у вас уже есть отчет, используйте его совместно с другими пользователями на сайте PowerBI.com.

При публикации отчета из Power BI Desktop на сайт PowerBI.com он создает **отчет** и **набор данных** в клиенте PowerBI.com. Например, после публикации на сайте PowerBI.com отчета **PowerBITutorial** его имя отобразится в разделах **Отчеты** и **Наборы данных** на PowerBI.com.

   :::image type="content" source="./media/powerbi-visualize/powerbi-reports-datasets.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI":::

Чтобы создать панель мониторинга для совместного использования, нажмите в своем отчете на PowerBI.com кнопку **Закрепление живой плитки** .

   :::image type="content" source="./media/powerbi-visualize/power-bi-pin-live-tile.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI":::

Для создания новой панели мониторинга следуйте инструкциям в разделе [Закрепление плиток из отчета](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) . 

Можно также внести в отчет ситуативные изменения, прежде чем создавать панель мониторинга. Однако для внесения изменений и повторной публикации отчета на PowerBI.com рекомендуется использовать Power BI Desktop.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

:::image type="content" source="./media/powerbi-visualize/power-bi-refresh-now.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI":::

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    :::image type="content" source="./media/powerbi-visualize/power-bi-schedule-refresh.png" alt-text="Представление отчета в Power BI Desktop — соединитель Power BI":::
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о Power BI см. в статье [Приступая к работе с Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Дополнительные сведения об Azure Cosmos DB см. на [целевой странице документации по Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

