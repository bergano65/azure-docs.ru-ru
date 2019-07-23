---
title: Руководство по ASP.NET Core MVC для Azure Cosmos DB. Разработка веб-приложений
description: Это руководство по MVC Core для ASP.NET поможет вам создать веб-приложение MVC с использованием Azure Cosmos DB. С помощью этого руководства по ASP NET Core MVC вы сможете сохранить файл JSON и получить доступ к данным из приложения "Список дел", размещенного в Службе приложений Azure.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: sngun
ms.openlocfilehash: 85d9cbe7d0807ca0e7951e1e12d1edbbf7c921db
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985889"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Руководство по Разработка веб-приложения ASP.NET Core MVC с использованием Azure Cosmos DB с помощью пакета SDK для .NET 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


В этом руководстве показано, как использовать Azure Cosmos DB для хранения данных и обеспечения доступа к ним из приложения ASP.NET MVC, размещенного в Azure. В этом руководстве используется пакет SDK для .NET версии 3. На следующем рисунке показана веб-страница, которая создается с помощью примера в этой статье:
 
![Снимок экрана: список дел веб-приложения MVC, созданный с помощью этого руководства по ASP.NET Core MVC](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

Если у вас нет времени для работы с руководством, можно скачать полный пример проекта с [GitHub][GitHub].

Темы, рассматриваемые в этом руководстве:

> [!div class="checklist"]
> * Создание учетной записи Azure Cosmos.
> * Создание приложения ASP.NET Core MVC
> * Подключение приложения к Azure Cosmos DB. 
> * Выполнение операций CRUD с данными.

> [!TIP]
> Предполагается, что у вас есть опыт использования ASP.NET Core MVC и службы приложений Azure. Если вы никогда не работали с ASP.NET Core или [необходимыми инструментами](#prerequisites), мы рекомендуем скачать полный пример проекта с портала [GitHub][GitHub], добавить требуемые пакеты NuGet и запустить его. Когда создадите проект, вы можете просмотреть эту статью, чтобы разобраться в коде этого проекта.

## <a name="prerequisites"></a>Предварительные требования 

Перед выполнением инструкций, приведенных в этой статье, обеспечьте наличие следующих ресурсов:

* **Активная учетная запись Azure**. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Все снимки экранов в этой статье сделаны с помощью Microsoft Visual Studio Community 2019. Если система настроена с помощью другой версии, то вполне вероятно, что ваши экраны и параметры могут не соответствовать полностью, но если выполнить все вышеуказанные требования, то это решение должно работать.

## <a name="create-an-azure-cosmos-account"></a>Шаг 1. Создание учетной записи Azure Cosmos

Давайте сначала создадим учетную запись Azure Cosmos. Если у вас уже есть учетная запись API SQL Azure Cosmos DB или вы используете эмулятор Azure Cosmos DB для этого руководства, то вы можете перейти к разделу [Создание приложения ASP.NET MVC](#create-a-new-mvc-application).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

В следующем разделе вы создадите приложение ASP.NET Core MVC. 

## <a name="create-a-new-mvc-application"></a>Шаг 2. Создание приложения ASP.NET Core MVC

1. В Visual Studio откройте меню **Файл** и выберите **Создать**, а затем — **Проект**. Откроется диалоговое окно **Новый проект** .

2. В окне **Новый проект**  используйте поле ввода **Поиск шаблонов**, чтобы выполнить поиск "Веб", а затем выберите **Веб-приложение ASP.NET Core**. 

   ![Создание проекта веб-приложения ASP.NET Core](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. В поле **Имя** введите имя проекта. В этом учебнике используется имя todo. Если вы решили использовать другое имя, в ситуациях, когда будет идти речь о пространстве имен todo, откорректируйте представленные образцы кода, используя имя своего приложения. 

4. Выберите **Обзор**, чтобы перейти к папке, в которой вы хотите создать проект. Нажмите кнопку **Создать**. 

5. Появится диалоговое окно **Создание нового веб-приложения ASP.NET Core**. В списке шаблонов выберите **Веб-приложение (модель-представление-контроллер)** .

6. Выберите **Создать**, и Visual Studio сформирует пустой шаблон ASP.NET Core MVC. 

7. После того как Visual Studio закончит создание шаблонного приложения MVC, у вас будет пустое приложение ASP.NET, которое можно запустить локально.

## <a name="add-nuget-packages"></a>Шаг 3. Добавление пакета Azure Cosmos DB NuGet в проект

Теперь, когда у нас есть большая часть кода платформы ASP.NET Core MVC, необходимого для этого решения, давайте добавим пакеты NuGet, требуемые для подключения к Azure Cosmos DB.

1. Пакет .NET SDK для Azure Cosmos DB комплектуется и распространяется как пакет NuGet. Чтобы получить пакет NuGet в Visual Studio, используйте диспетчер пакетов NuGet в Visual Studio: щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Управление пакетами NuGet**.
   
   ![Снимок экрана: параметры контекстного меню проекта веб-приложения в обозревателе решений с выделенным параметром "Управление пакетами NuGet"](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
2. Откроется диалоговое окно **Управление пакетами NuGet** . В поле **обзора** NuGet введите **Microsoft.Azure.Cosmos**. В результатах поиска выберите пакет **Microsoft.Azure.Cosmos** и установите его. Загрузится и установится пакет Azure Cosmos DB, а также все зависимости. Чтобы завершить установку, выберите **Я принимаю** в окне **Принятие условий лицензионного соглашения**.
   
   Для установки пакета NuGet можно также воспользоваться консолью диспетчера пакетов. Для этого в меню **Инструменты** выберите **Диспетчер пакетов NuGet**, а затем — **Консоль диспетчера пакетов**. При появлении запроса введите следующую команду:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos
   ```        

3. После установки пакета проект Visual Studio будет содержать ссылку на библиотеку Microsoft.Azure.Cosmos.
  
## <a name="set-up-the-mvc-application"></a>Шаг 4. Настройка приложения ASP.NET Core MVC

Теперь добавим модели, представления и контроллеры в это приложение MVC.

* [Добавление модели](#add-a-model).
* [Добавление контроллера](#add-a-controller).
* [Добавление представлений](#add-views).

### <a name="add-a-model"></a> Добавление модели

1. В **обозревателе решений** щелкните правой кнопкой мыши папку **Модели**, выберите **Добавить**, а затем щелкните **Класс**. Откроется диалоговое окно **Добавление нового элемента** .

1. Назовите новый класс **Item.cs** и выберите **Добавить**. 

1. Далее замените код в классе "Item.cs" следующим кодом.

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs)]
   
   Хранимые данные в Azure Cosmos DB передаются по сети и сохраняются в виде JSON-файлов. Для управления сериализацией и десериализацией объектов с помощью JSON.NET можно использовать атрибут **JsonProperty**, как показано на примере созданного класса **Item**. Вы можете не только контролировать формат имени свойства при его поступлении в JSON, но также и переименовывать свойства .NET, как вы сделали со свойством **Completed**. 

### <a name="add-a-controller"></a>Добавление контроллера

1. В **обозревателе решений** щелкните правой кнопкой мыши папку **Контроллеры**, выберите **Добавить**, а затем щелкните **Контроллер**. Откроется диалоговое окно **Добавление элемента формирования шаблонов** .

1. Выберите **Контроллер MVC — пустой** и нажмите кнопку **Добавить**.

   ![Снимок экрана: диалоговое окно "Добавление шаблона" с выделенным параметром "Контроллер MVC — пустой"](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Назовите новый контроллер **ItemController** и замените код в этом файле следующим:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs)]

   Атрибут **ValidateAntiForgeryToken** используется здесь для защиты этого приложения от атак с подделкой межсайтовых запросов. Кроме добавления этого атрибута представления также должны работать с данным маркером защиты от подделки запросов. Дополнительные сведения об этом и примеры правильной реализации этой технологии см. в статье [Preventing Cross-Site Request Forgery (CSRF) Attacks in ASP.NET MVC Application][Preventing Cross-Site Request Forgery]. The source code provided on [GitHub][GitHub] (Предотвращение атак с подделкой межсайтовых запросов в приложениях ASP.NET MVC) имеет полную реализацию.

   Для защиты от атак overposting также используется атрибут **Bind** в параметре метода. Дополнительные сведения см. в записи блога [Implementing Basic CRUD Functionality with the Entity Framework in ASP.NET MVC Application][Basic CRUD Operations in ASP.NET MVC] (Реализация базовых функций CRUD с помощью Entity Framework в приложении ASP.NET MVC).

### <a name="add-views"></a>Добавление представлений

Теперь давайте создадим следующие три представления. 

* [Добавление представления "Элемент списка"](#AddItemIndexView).
* [Добавление представления "Создание элементов"](#AddNewIndexView).
* [Добавление представления "Редактирование элементов"](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Добавление представления "Элемент списка"

1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши пустую папку **Элемент**, созданную Visual Studio при добавлении **ItemController** ранее, выберите **Добавить**, а затем щелкните **Представление**.
   
   ![Снимок экрана: обозреватель решений, отображающий папку "Элемент", созданную Visual Studio, с выделенными командами "Добавления представления"](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)

2. В диалоговом окне **Добавление представления** обновите следующие значения.
   
   * В поле **Имя представления** введите ***Индекс***.
   * В поле **Шаблон** выберите ***Список***.
   * В поле **Класс модели** выберите ***Item (todo.Models)*** (Элемент (todo.Models)).
   * В поле страницы макета введите ***~/Views/Shared/_Layout.cshtml***.
     
   ![Снимок экрана: диалоговое окно "Добавление представления"](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)

3. После добавления этих значений выберите **Добавить**, и Visual Studio создаст представление. После этого откроется созданный CSHTML-файл. Вы можете закрыть этот файл в Visual Studio и вернуться к нему позже.

#### <a name="AddNewIndexView"></a>Добавление представления "Создание элементов"

Аналогично тому, как вы создали представление для вывода списка элементов, создайте представление для создания элементов, выполнив следующие действия:

1. В **обозревателе решений**, щелкните правой кнопкой мыши папку **Элемент**, выберите **Добавить**, а затем щелкните **Представление**.

1. В диалоговом окне **Добавление представления** обновите следующие значения.
   
   * В поле **Имя представления** введите ***Создать***.
   * В поле **Шаблон** выберите ***Создать***.
   * В поле **Класс модели** выберите ***Item (todo.Models)*** (Элемент (todo.Models)).
   * В поле страницы макета введите ***~/Views/Shared/_Layout.cshtml***.
   * Выберите **Добавить**.
   
#### <a name="AddEditIndexView"></a>Добавление представления "Редактирование элементов"

И наконец, добавьте представление для редактирования элемента, сделав следующее:

1. В **обозревателе решений**, щелкните правой кнопкой мыши папку **Элемент**, выберите **Добавить**, а затем щелкните **Представление**.

1. В диалоговом окне **Добавление представления** выполните следующие действия.
   
   * В поле **Имя представления** введите ***Изменить***.
   * В поле **Шаблон** выберите ***Изменить***.
   * В поле **Класс модели** выберите ***Item (todo.Models)*** (Элемент (todo.Models)).
   * В поле страницы макета введите ***~/Views/Shared/_Layout.cshtml***.
   * Выберите **Добавить**.

Как только все будет готово, закройте документы cshtml в Visual Studio, вы вернетесь к ним позже.

## <a name="connect-to-cosmosdb"></a>Шаг 5. Подключение к Azure Cosmos DB 

Теперь, когда мы позаботились об основных ресурсах MVC, давайте рассмотрим добавление кода для подключения к Azure Cosmos DB и выполнения операций CRUD. 

### <a name="perform-crud-operations"></a> Выполнение операций CRUD с данными

Прежде всего следует добавить класс, который содержит необходимую логику для подключения к службе Azure Cosmos DB и ее использования. В этом руководстве мы инкапсулируем эту логику в класс с именем `CosmosDBService` и интерфейсом с именем `ICosmosDBService`. Эта служба выполняет CRUD и операции веб-каналов, такие как отображение незавершенных элементов, а также создание, редактирование и удаление элементов. 

1. В **обозревателе решений** создайте папку в проекте с именем **Services**.

1. Щелкните правой кнопкой мыши папку **Services**, выберите **Добавить**, а затем — **Класс**. Назовите новый класс **CosmosDBService** и выберите **Добавить**.

1. Добавьте следующий код в класс **CosmosDBService** и замените код в этом файле на такой:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs)]

1. Повторно выполните 2-3 этап, но на этот раз для класса с именем **ICosmosDBService**, и добавьте указанный ниже код.

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs)]
 
1. Предыдущий код получает `CosmosClient` как часть конструктора. После конвейера ASP.NET Core нужно перейти в **Startup.cs** проекта и инициализировать клиента на основе конфигурации как одноэлементный экземпляр, который будет внедрен с помощью [внедрения зависимостей](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). В обработчике **ConfigureServices** определяется:

    ```csharp
    services.AddSingleton<ICosmosDbService>(InitializeCosmosClientInstanceAsync(Configuration.GetSection("CosmosDb")).GetAwaiter().GetResult());
    ```

1. В этом же файле определяется вспомогательный метод **InitializeCosmosClientInstanceAsync**, с помощью которого выполняется чтение конфигурации, а также инициализация клиента.

    ```csharp
    private static async Task<CosmosDbService> InitializeCosmosClientInstanceAsync(IConfigurationSection configurationSection)
    {
        string databaseName = configurationSection.GetSection("DatabaseName").Value;
        string containerName = configurationSection.GetSection("ContainerName").Value;
        string account = configurationSection.GetSection("Account").Value;
        string key = configurationSection.GetSection("Key").Value;
        CosmosClientBuilder clientBuilder = new CosmosClientBuilder(account, key);
        CosmosClient client = clientBuilder
                            .WithConnectionModeDirect()
                            .Build();
        CosmosDbService cosmosDbService = new CosmosDbService(client, databaseName, containerName);
        Database database = await client.CreateDatabaseIfNotExistsAsync(databaseName);
        await database.CreateContainerIfNotExistsAsync(containerName, "/id");

        return cosmosDbService;
    }
    ```

1. Конфигурация определяется в файле проекта **appsettings.json**. Откройте его и добавьте раздел с именем **CosmosDb**.

   ```csharp
     "CosmosDb": {
        "Account": "<enter the URI from the Keys blade of the Azure Portal>",
        "Key": "<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>",
        "DatabaseName": "Tasks",
        "ContainerName": "Items"
      }
   ```
 
Теперь, если вы запустите приложение, конвейер ASP.NET Core будет создавать экземпляр **CosmosDbService** и поддерживать один экземпляр как одноэлементный. Когда **ItemController** используется для обработки запросов на стороне клиента, он получает этот отдельный экземпляр и сможет использовать его, чтобы выполнять операции CRUD.

Если создать и запустить этот проект сейчас, отобразятся примерно следующие данные.

![Снимок экрана: веб-приложение "Список дел", созданное с помощью этого учебника по базе данных](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Шаг 6. Локальный запуск приложения

Для проверки приложения на локальном компьютере выполните следующие действия:

1. Чтобы создать приложение в режиме отладки, откройте Visual Studio и нажмите клавишу F5. После этого будет создано приложение и откроется окно браузера с пустой сеткой, которую мы уже видели:
   
   ![Снимок экрана: веб-приложение "Список дел", созданное с помощью этого руководства](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Щелкните ссылку **Создать**, введите значения в поля **Имя** и **Описание**. Не устанавливайте флажок **Выполнено**, иначе новый элемент добавится ​​в завершенном состоянии и не отобразится в начальном списке.
   
3. Щелкните **Создать**. Вы будете перенаправлены в представление **Индекс**, а в списке появится ваш элемент. Вы можете добавить еще несколько элементов в список задач.

    ![Снимок экрана: представление "Индекс"](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
  
4. Нажмите кнопку **Изменить** рядом с **Элемент**. Вы будете перенаправлены в представление **Изменить**, где сможете изменить любое свойство объекта, в том числе флажок **Выполнено**. Если установить флажок **Выполнено** и нажать кнопку **Сохранить**, **Элемент** будет отображен в списке как завершенный.
   
   ![Снимок экрана: представление "Индекс" с установленным флажком возле параметра "Завершено"](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)

5. В любой момент можно проверить состояние данных в службе Azure Cosmos DB с помощью [обозревателя Cosmos](https://cosmos.azure.com) или обозревателя данных эмулятора Azure Cosmos DB.

6. После проверки приложения нажмите клавиши CONTROL+F5, чтобы остановить отладку приложения. Теперь все готово к развертыванию.

## <a name="deploy-the-application-to-azure"></a>Шаг 7. Развертывание приложения 
Теперь, когда у вас есть готовое приложение, которое корректно работает в Azure Cosmos DB, мы собираемся развернуть его в службу приложений Azure.  

1. Чтобы опубликовать это приложение, щелкните проект правой кнопкой мыши в **обозревателе решений** и выберите действие **Опубликовать**.
   
2. В диалоговом окне **Публикация** выберите **Служба приложений**, затем выберите **Создать**, чтобы создать профиль службы приложений, или щелкните **Выбрать существующий**, чтобы использовать имеющийся профиль.

3. При наличии профиля Службы приложений Azure выберите **подписку** из раскрывающегося списка. Используйте фильтр **Просмотр** для сортировки по группе ресурсов или типу ресурсов. Затем выполните поиск необходимой Службы приложений Azure и нажмите кнопку **ОК**.
   
   ![Диалоговое окно "Служба приложений" в Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Чтобы создать профиль службы приложений Azure, в диалоговом окне **Публикация** выберите **Создать**. В диалоговом окне **Создать службу приложений** введите имя веб-приложения и соответствующую подписку, группу ресурсов и план службы приложений, а затем нажмите кнопку **Создать**.

   ![Диалоговое окно "Создать службу приложений" в Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

Через несколько секунд Visual Studio опубликует ваше веб-приложение и запустит браузер, где вы увидите свой проект, запущенный в Azure!

## <a name="next-steps"></a>Дополнительная информация
В этом руководстве вы узнали, как создать веб-приложение ASP.NET Core MVC, которое может получить доступ к хранимым данным в Azure Cosmos DB. Теперь вы можете перейти к следующей статье:

* [Partitioning in Azure Cosmos DB](./partitioning-overview.md) (Секционирование в Azure Cosmos DB)
* [Getting started with SQL queries](./how-to-sql-query.md) (Начало работы с запросами SQL)
* [How to model and partition data on Azure Cosmos DB using a real-world example](./how-to-model-partition-example.md) (Моделирование и секционирование данных в базе данных Azure Cosmos на реальных примерах)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
