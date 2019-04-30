---
title: Руководство по Power BI для соединителя Azure Cosmos DB
description: Используйте это руководство по Power BI для импорта JSON, создания информативных отчетов и визуализации данных с помощью соединителя Azure Cosmos DB и Power BI.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: sngun
ms.openlocfilehash: 2c58b982e596c95aa47442c1897410fe9ab6b99a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60929829"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Визуализация данных Azure Cosmos DB с помощью соединителя Power BI

[Power BI](https://powerbi.microsoft.com/) — это веб-служба, в которой можно создавать и совместно использовать панели мониторинга и отчеты. Power BI Desktop — это средство разработки отчетов, позволяющее получать данные из различных источников данных. Azure Cosmos DB является одним из источника данных, который можно использовать с Power BI Desktop. Power BI Desktop можно подключить к учетной записи Azure Cosmos DB с помощью соединителя Azure Cosmos DB для Power BI.  После импорта в Power BI данные Azure Cosmos DB можно преобразовывать, создавать отчеты и публиковать их в Power BI.   

В этой статье описываются шаги, необходимые для подключения учетной записи Azure Cosmos DB к Power BI Desktop. После подключения перейдите к коллекции, извлеките данные, преобразуйте данные JSON в табличный формат и опубликуйте отчет в Power BI.

> [!NOTE]
> Соединитель Power BI для Azure Cosmos DB подключается к Power BI Desktop. Отчеты, созданные в Power BI Desktop, можно опубликовать на сайте PowerBI.com. Напрямую извлекать данные Azure Cosmos DB с PowerBI.com невозможно. 

> [!NOTE]
> Подключение к Azure Cosmos DB с помощью соединителя Power BI в настоящее время поддерживается только для учетных записей API SQL Azure Cosmos DB и Gremlin API.

## <a name="prerequisites"></a>Технические условия
Перед выполнением инструкций в этом руководстве по Power BI убедитесь в наличии доступа к следующим ресурсам:

* [загруженной последней версии Power BI Desktop](https://powerbi.microsoft.com/desktop);

* загруженному из GitHub [примеру данных о вулканах](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json);

* [созданной учетной записи базы данных Azure Cosmos DB](https://azure.microsoft.com/documentation/articles/create-account/) и импортированных с помощью [средства переноса данных Azure Cosmos DB](import-data.md) данных о вулканах. При импорте данных советуем использовать в средстве переноса следующие параметры источника и целевых расположений:

   * **Параметры источника** 

       * **Импортировать из:** JSON-файлы

   * **Параметры целевых расположений** 

      * **Строка подключения:** `AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Ключ секции**: /страна 

      * **Collection throughput** (Пропускная способность коллекции): 1000 

Для совместного использования отчетов в службе PowerBI.com необходимо настроить учетную запись в PowerBI.com.  Дополнительные сведения о Power BI и Power BI Pro см. по ссылке [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Приступим
Для работы с этим руководством представим, что вы геолог, изучающий вулканы по всему миру. Данные о вулканах хранятся в учетной записи Azure Cosmos DB, а формат документа JSON выглядит следующим образом.

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

Вам нужно получить данные о вулканах из учетной записи Azure Cosmos DB и представить их в виде интерактивного отчета Power BI.

1. Запустите Power BI Desktop.

2. Непосредственно на экране приветствия можно **получить данные**, просмотреть **последние источники** или **открыть другие отчеты**. Щелкните значок X в правом верхнем углу, чтобы закрыть этот экран. Откроется представление **Отчет** средства Power BI Desktop.
   
   ![Представление отчета в Power BI Desktop — соединитель Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview.png)

3. Откройте ленту **Главная** и щелкните **Получить данные**.  Откроется окно **Получение данных** .

4. Выберите **Azure**, затем — **Azure Cosmos DB (бета-версия)** и щелкните **Подключить**. 

    ![Получение данных в Power BI Desktop — соединитель Power BI](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   

5. На странице **Предварительная версия соединителя** нажмите кнопку **Продолжить**. Откроется окно **Azure Cosmos DB**.

6. Укажите URL-адрес конечной точки учетной записи Azure Cosmos DB, из которой необходимо получить данные, как показано ниже, и нажмите кнопку **ОК**. Чтобы использовать собственную учетную запись, можно найти URL-адрес в поле универсального кода ресурса (URI) в колонке **Ключи** на портале Azure. При необходимости можно задать имя базы данных, имя коллекции или использовать навигатор для выбора базы данных и коллекции, чтобы определить источник данных.
   
7. При подключении к этой конечной точке в первый раз отображается запрос на введение ключа учетной записи. Для собственной учетной записи найдите ключ в поле **Первичный ключ** в колонке **Ключи только для чтения** на портале Azure. Введите соответствующий ключ и нажмите кнопку **Подключить**.
   
   При создании отчетов мы рекомендуем использовать ключ только для чтения. Это защитит главный ключ от ненужных рисков безопасности. Ключ только для чтения доступен в колонке **Ключи** на портале Azure. 
    
8. После успешного подключения учетной записи откроется панель **Навигатор**. На панели **Навигатор** отображается список баз данных в учетной записи.

9. Выберите и разверните узел базы данных, из которого поступают данные для отчета, а затем выберите **volcanodb** (имя базы данных может отличаться).   

10. Теперь выберите коллекцию, содержащую данные для извлечения, и выберите **volcano1** (имя коллекции может отличаться).
    
    В области «Предварительный просмотр» отображается список элементов **Запись** .  Документ представлен как тип **Запись** в Power BI. Точно так же вложенный блок JSON в документе является **Записью**.
    
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — окно навигатора](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Нажмите кнопку **Изменить**, чтобы открыть в новом окне редактор запросов для преобразования данных.

## <a name="flattening-and-transforming-json-documents"></a>Преобразование документов JSON в плоскую структуру и их трансформация
1. Переключитесь в окно редактора запросов Power BI, где по центру отображается столбец **Документ**.
   ![Редактор запросов Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. В правой части заголовка столбца **Документ** щелкните кнопку развертывания.  Отобразится контекстное меню со списком полей.  Выберите поля, необходимые для вашего отчета, например "Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status" и "Last Know Eruption", а затем нажмите кнопку "ОK". Снимите флажок **Использовать исходное имя столбца как префикс** и щелкните **ОК**.
   
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — раскрыть документы](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. В центральной области отображается предварительный просмотр результата с выбранными полями.
   
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — сделать результаты более плоскими](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. В нашем примере свойство «Расположение» является блоком GeoJSON в документе.  Как видно, значение свойства «Расположение» представлено типом **Запись** в Power BI Desktop.  
5. Щелкните элемент развертывания справа от заголовка столбца Document.Location.  Отобразится контекстное меню с полями типа и координат.  Выберите здесь поле координат, снимите флажок **Использовать исходное имя столбца как префикс** и щелкните **ОК**.
   
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — запись о расположении](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. Теперь в центральной области отображается столбец с координатами типа **Список** .  Как показано в начале этого руководства, данные GeoJSON в руководстве принадлежат типу «Точка» со значениями широты и долготы, записанными в массив координат.
   
    Элемент coordinates[0] представляет долготу, а элемент coordinates[1] — широту.
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — список координат](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Чтобы преобразовать массив координат в плоскую структуру, создайте **настраиваемый столбец** с именем LatLong.  Выберите **Добавление столбца** на ленте и щелкните **Настраиваемый столбец**.  Откроется окно **Настраиваемый столбец**.
8. Укажите имя нового столбца, например LatLong.
9. Затем укажите настраиваемую формулу для нового столбца.  В нашем примере мы соединим значения широты и долготы, разделяя их запятыми, как показано ниже, используя следующую формулу: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Последовательно выберите **ОК**.
   
    Дополнительные сведения о выражений анализа данных (DAX) включая функции DAX, см. на [основные сведения о DAX в Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-learn-dax-basics).
   
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — добавление пользовательского столбца](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Теперь в центральной области отображается новый столбец LatLong, заполненный значениями.
    
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — пользовательский столбец LatLong](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Если в новом столбце появляется сообщение об ошибке, убедитесь, что примененные шаги в разделе "Параметры запроса" соответствуют указанным на следующем рисунке:
    
    ![Примененные шаги должны быть такими: источник, навигация, развернут документ, развернуто расположение документа, добавлен настраиваемый элемент](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Если ваши шаги отличаются, удалите лишние шаги и снова попробуйте добавить настраиваемый столбец. 

11. Чтобы сохранить модель данных, нажмите кнопку **Закрыть и применить** .
    
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — закрыть и применить](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Создание отчетов
Для создания отчетов, в которых наглядно представлены данные, используется представление отчета в Power BI Desktop.  Отчеты можно создавать путем перетаскивания полей на поле **Отчет** .

![Представление отчета в Power BI Desktop — соединитель Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

В представлении «Отчет» найдите следующие элементы.

1. В области **Поля** отображается список моделей данных с перечислением полей, которые можно использовать для отчетов.
2. Область **Визуализации** . Отчет может содержать одну или несколько визуализаций.  В области **Визуализации** можно выбрать нужные типы визуальных элементов.
3. Холст **Отчет** позволяет создавать визуальные элементы для отчета.
4. Страница **Отчеты** . В Power BI Desktop можно добавить несколько страниц отчета.

Ниже приведены основные этапы создания простого интерактивного отчета с представлением карты.

1. В этом примере мы создадим представление карты, показывающей расположение каждого вулкана.  В области **Визуализации** выберите тип визуального элемента "Карта", который выделен на снимке экрана выше.  В области **Отчет** отобразится тип визуального элемента «Карта».  В области **Визуализация** также должен отобразиться набор свойств, связанных с этим типом визуального элемента.
2. Теперь из области **Поля** перетащите поле "LatLong" в свойство **Location** в области **Визуализации**.
3. Затем перетащите поле «Имя вулкана» в свойство **Условные обозначения** .  
4. Затем перетащите поле "Высота" в свойство **Размер** .  
5. Теперь на карте должны отображаться кружки, указывающие расположение каждого вулкана (размер пузырька соответствует высоте вулкана).
6. Базовый отчет готов.  Его можно настроить, добавив дополнительные визуальные элементы.  В нашем случае мы добавили срез «Тип вулкана», чтобы сделать отчет интерактивным.  
   
7. В меню "Файл" щелкните **Сохранить** и сохраните файл с именем PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Публикация и совместное использование отчета
Для совместного использования отчета необходимо иметь учетную запись в службе PowerBI.com.

1. В средстве Power BI Desktop откройте ленту **Главная** .
2. Нажмите кнопку **Опубликовать**.  Вы увидите запрос на ввод имени пользователя и пароля учетной записи PowerBI.com.
3. После аутентификации учетных данных отчет публикуется в указанном вами расположении.
4. Для просмотра и совместного использования отчета на сайте PowerBI.com щелкните **Открыть PowerBITutorial.pbix в Power BI** .
   
    ![Публикация в Power BI успешно завершена! Открыть учебник в Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Создание панели мониторинга на PowerBI.com
Теперь, когда у вас уже есть отчет, используйте его совместно с другими пользователями на сайте PowerBI.com.

При публикации отчета из Power BI Desktop на сайт PowerBI.com он создает **отчет** и **набор данных** в клиенте PowerBI.com. Например, после публикации на сайте PowerBI.com отчета **PowerBITutorial** его имя отобразится в разделах **Отчеты** и **Наборы данных** на PowerBI.com.

   ![Снимок экрана нового отчета и набора данных на PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Чтобы создать панель мониторинга для совместного использования, нажмите в своем отчете на PowerBI.com кнопку **Закрепление живой плитки** .

   ![Снимок экрана нового отчета и набора данных на PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Для создания новой панели мониторинга следуйте инструкциям в разделе [Закрепление плиток из отчета](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) . 

Можно также внести в отчет ситуативные изменения, прежде чем создавать панель мониторинга. Однако для внесения изменений и повторной публикации отчета на PowerBI.com рекомендуется использовать Power BI Desktop.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

![Screenshot of Refresh Now in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    ![Screenshot of the Schedule Refresh in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
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

