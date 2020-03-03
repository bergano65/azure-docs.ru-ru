---
title: Руководство по Индексирование данных на языке C# из баз данных SQL Azure
titleSuffix: Azure Cognitive Search
description: В этом учебнике по C# описано, как подключить базу данных SQL Azure, извлечь доступные для поиска данные и отправить их в индекс в службе "Когнитивный поиск Azure".
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: 978587b68e719b79db31ff25adaf2b38d2235095
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650103"
---
# <a name="tutorial-index-azure-sql-data-in-c-using-azure-cognitive-search-indexers"></a>Руководство по Импорт данных из SQL Azure на языке C# с использованием индексаторов Когнитивного поиска Azure

Используя C#, вы можете настроить [индексатор](search-indexer-overview.md), который извлекает из базы данных Azure SQL данные с поддержкой поиска и отправляет их в индекс поиска. В этом руководстве [клиентские библиотеки .NET Когнитивного поиска Azure](https://aka.ms/search-sdk) и консольное приложение .NET Core используются для выполнения следующих задач:

> [!div class="checklist"]
> * создание источника данных, который подключается к Базе данных SQL Azure;
> * настройка индексатора;
> * выполнение индексатора для загрузки данных в индекс;
> * обращение к индексу для проверки.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

+ [База данных SQL Azure](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Создайте службу поиска](search-create-service-portal.md) или [найдите существующую службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Для выполнения инструкций из этого руководства вы можете использовать бесплатную версию службы. В бесплатной версии вы можете использовать не более трех индексов, трех индексаторов и трех источников данных. В этом руководстве создается по одному объекту из каждой категории. Перед началом работы убедитесь, что у службы есть достаточно места, чтобы принять новые ресурсы.

## <a name="download-source-code"></a>Скачивание исходного кода

Исходный код для этого руководства размещен в папке [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) репозитория [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) на GitHub.

## <a name="get-a-key-and-url"></a>Получение ключа и URL-адреса

Для вызова API требуется URL-адрес службы и ключ доступа. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Когнитивный поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **обзора** службы поиска получите URL-адрес. Пример конечной точки может выглядеть так: `https://mydemo.search.windows.net`.

1. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

   ![Получение конечной точки HTTP и ключа доступа](media/search-get-started-postman/get-url-key.png "Получение конечной точки HTTP и ключа доступа")

## <a name="set-up-connections"></a>Настройка подключений

1. Запустите Visual Studio и откройте файл **DotNetHowToIndexers.sln**.

1. Откройте в обозревателе решений файл **appsettings.json** и замените заполнители реальными значениями для подключения к службе поиска. Если полный URL-адрес имеет вид https://my-demo-service.search.windows.net, значит нужно указать имя службы my-demo-service.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
    }
    ```

Последняя запись требует существование базы данных. Вы создадите ее на следующем шаге.

## <a name="prepare-sample-data"></a>Подготовка примера данных

На этом этапе в Базе данных SQL Azure создается внешний источник данных, который может сканироваться индексатором. Чтобы создать набор данных в базе данных Azure SQL, вы можете использовать портал Azure и файл *hotels.sql*. В службе "Когнитивный поиск Azure" используются плоские наборы строк. Такие наборы обычно создаются на основе представления или запроса. При помощи файла SQL в примере решения создается и заполняется одна таблица.

Если у вас уже есть ресурс в Базе данных SQL Azure, можно добавить в него таблицу отелей, сразу перейдя к шагу 4.

1. [Войдите на портал Azure](https://portal.azure.com/).

1. Щелкните или создайте **Базу данных SQL**. Вы можете использовать значения по умолчанию и самую низкую ценовую категорию. Одним из преимуществ создания сервера является то, что вы можете указать имя пользователя и пароль администратора, которые потребуются для создания и загрузки таблиц в дальнейшем.

   ![Страница создания базы данных](./media/search-indexer-tutorial/indexer-new-sqldb.png "Страница создания базы данных")

1. Щелкните **Проверить и создать**, чтобы развернуть новый сервер и базу данных. Дождитесь, пока завершится развертывание сервера и базы данных.

1. В области навигации щелкните **Редактор запроса (предварительная версия)** , а затем введите имя пользователя и пароль администратора сервера. 

   Если доступ запрещен, скопируйте IP-адрес клиента из сообщения об ошибке, а затем щелкните ссылку **Настройка брандмауэра для сервера**, чтобы добавить правило, которое разрешает доступ с клиентского компьютера по определенному IP-адресу. Новое правило может вступить в силу через несколько минут.

1. В редакторе запросов выберите действие **Открыть запрос** и перейдите к файлу *hotels.sql* на локальном компьютере. 

1. Выберите файл и нажмите **Открыть**. Скрипт должен выглядеть, как на следующем снимке экрана:

   ![Скрипт SQL](./media/search-indexer-tutorial/sql-script.png "Скрипт SQL")

1. Нажмите **Выполнить** для выполнения запроса. В области результатов появится сообщение об успешном выполнении запроса для 3 строк.

1. Чтобы получить набор строк из этой таблицы, можно выполнить следующий запрос для проверки:

    ```sql
    SELECT * FROM Hotels
    ```

1. Скопируйте строку подключения ADO.NET к базе данных. Щелкните **Параметры** > **Строки подключения** и скопируйте строку подключения ADO.NET, как показано в примере ниже.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

1. Вставьте строку подключения в AzureSqlConnectionString в качестве третьей записи файла **appsettings.json** в Visual Studio.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

1. Введите пароль в строку подключения в файле **appsettings.json**. Имена базы данных и пользователя будут переданы через строку подключения, а пароль нужно будет ввести вручную.

## <a name="build-the-solution"></a>Выполните сборку решения.

Нажмите клавишу F5, чтобы скомпилировать решение. Программа выполняется в режиме отладки. В окне консоли отображаются сведения о состоянии каждой операции.

   ![Выходные данные консоли](./media/search-indexer-tutorial/console-output.png "Вывод на консоль")

Код выполняется локально в Visual Studio. Устанавливается подключение к службе поиска в Azure, которая в свою очередь подключается к Базе данных SQL Azure, чтобы получить набор данных. При таком большом количестве операций есть несколько возможных точек сбоя. Если поступает сообщение об ошибке, прежде всего проверьте следующее:

+ Сведения о подключении для службы поиска, которые следует указать, ограничены именем службы в этом руководстве. Если вы ввели полный URL-адрес, когда создается индекс, выполнение операций прекращается и отображается сообщение о сбое подключения.

+ Сведения о подключении к базе данных в **appsettings.json**. Это должна быть строка подключения ADO.NET, полученная на портале, в которую добавлены допустимые для базы данных имя пользователя и пароль. Учетная запись пользователя должна предоставлять разрешение на получение данных. Должен быть разрешен доступ с IP-адреса локального клиента.

+ Ограничения ресурсов. Помните, что для уровня "Бесплатный" действуют ограничения в три индекса, индексатора и источника данных. В службе с максимальным количеством объектов нельзя создавать новые.

## <a name="check-results"></a>Проверка результатов

Проверьте создание объекта на портале Azure и создайте запрос по индексу с помощью **обозревателя поиска**.

1. [Войдите на портал Azure](https://portal.azure.com/), откройте страницу **Обзор** для службы поиска и поочередно откройте каждый список, чтобы убедиться в том, что объект создан. В списках **Индексы**, **Индексаторы** и **Источники данных** должны присутствовать элементы hotels, azure-sql-indexer и azure-sql соответственно.

   ![Плитки для индексаторов и источников данных](./media/search-indexer-tutorial/tiles-portal.png)

1. Выберите индекс hotels. На странице индекса hotels на первой вкладке вы найдете **обозреватель поиска**. 

1. Щелкните **Поиск**, чтобы отправить пустой запрос. 

   В индексе возвращаются три записи в виде документов JSON. Проводник поиска возвращает документы в формате JSON, чтобы можно было просматривать всю структуру.

   ![Запрос индекса](./media/search-indexer-tutorial/portal-search.png "Запрос индекса")
   
1. Затем введите строку поиска `search=river&$count=true`. 

   При помощи этого запроса вызывается полнотекстовый поиск по слову `river`. В результате выводится количество соответствующих документов. Количество соответствующих документов полезно знать при тестировании, когда используется большой индекс с тысячами или миллионами документов. В этом случае только один документ соответствует запросу.

1. Наконец, введите строку поиска, которая ограничивает выходные данные JSON до требуемых полей: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   Ответ на запрос сокращается до выбранных полей, обеспечивая более точный результат.

## <a name="explore-the-code"></a>Обзор кода

Теперь, когда вы узнали, что именно создает пример кода, давайте вернемся в решение для анализа самого кода. Нужный нам код находится в двух файлах:

  + **hotel.cs** содержит схему, которая определяет индекс;
  + **Program.cs** содержит функции для создания структур и управления ими в службе.

### <a name="in-hotelcs"></a>hotell.cs

Схема индексов определяет коллекцию полей, включая атрибуты, в которых указаны разрешенные операции. Например, в них указывается, поддерживает ли схема полнотекстовый поиск, фильтрацию или сортировку, как в представленном ниже определении для HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Кроме того, схема может включать профили повышения для оценки результатов поиска, пользовательские анализаторы и другие конструкции. Но для наших целей схема определена не так подробно. Она содержит только поля, обнаруженные в примерах наборов данных.

### <a name="in-programcs"></a>Program.cs

Основная программа содержит логику для создания клиента, индекса, источника данных и индексатора. При помощи этого кода проверяется наличие ресурсов с таким же именем. При обнаружении такие ресурсы удаляются, так как есть вероятность, что вы будете запускать эту программу несколько раз.

Объект источника данных настраивается с помощью параметров, относящихся к ресурсам базы данных Azure SQL, включая [частичное или добавочное индексирование](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) для использования встроенных [функций обнаружения изменений](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) SQL Azure. Демонстрационная база данных гостиниц в SQL Azure содержит столбец "обратимого удаления", **IsDeleted**. Если этот столбец имеет значение true в базе данных, индексатор удаляет соответствующий документ из индекса Когнитивного поиска Azure.

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

Объект индексатора не зависит от платформы. Его настройка, планирование и вызов одинаковы независимо от источника. Этот пример индексатора включает в себя расписание, возможность сброса, которая очищает журнал индексатора и вызывает метод для создания и немедленного запуска индексатора.

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы работаете в своей подписке, после завершения проекта целесообразно удалить созданные ресурсы, которые вам больше не потребуются. Работающие ресурсы могут означать лишние затраты. Можно удалить отдельные ресурсы или удалить группу ресурсов, что позволит удалить весь набор ресурсов.

Просматривать ресурсы и управлять ими можно на портале с помощью ссылок "Все ресурсы" или "Группы ресурсов" в области навигации слева.

При работе с бесплатной версией службы помните о том, что вам доступно максимум три индекса, индексатора и источника данных. Вы можете удалить отдельные элементы на портале, чтобы не превысить лимит.

## <a name="next-steps"></a>Дальнейшие действия

В Когнитивном поиске Azure предоставляются индексаторы для нескольких источников данных Azure. На следующем шаге вы изучите индексатор для хранилища больших двоичных объектов Azure.

> [!div class="nextstepaction"]
> [Индексирование документов в хранилище BLOB-объектов Azure с помощью службы поиска Azure](search-howto-indexing-azure-blob-storage.md)