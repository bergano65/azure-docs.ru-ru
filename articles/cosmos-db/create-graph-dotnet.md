---
title: Создание приложения .NET Framework, Core с помощью API Gremlin для Azure Cosmos DB
description: В этой статье представлен пример кода .NET Framework или Core, который можно использовать для подключения и выполнения запросов к Azure Cosmos DB.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/21/2020
ms.author: lbosq
ms.openlocfilehash: f700b06e6ade0d72178777b67cb734f3120b36dc
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395465"
---
# <a name="quickstart-build-a-net-framework-or-core-application-using-the-azure-cosmos-db-gremlin-api-account"></a>Краткое руководство. Создание .NET Framework или основного приложения с помощью учетной записи API Azure Cosmos DB Gremlin

> [!div class="op_single_selector"]
> * [Консоль Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования базы данных Azure Cosmos DB. 

В этом кратком руководстве описано, как создать учетную запись [API Gremlin](graph-introduction.md) для Azure Cosmos DB, базу данных и граф (контейнер) с помощью портала Azure. Затем вы можете создать и запустить консольное приложение c помощью драйвера [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) с открытым кодом.  

## <a name="prerequisites"></a>Предварительные требования

Если вы еще не установили Visual Studio 2019, вы можете скачать и использовать **бесплатную** среду [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность **разработки для Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Добавление графа

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь необходимо клонировать приложение API Gremlin из GitHub, задать строку подключения и запустить приложение. Вы узнаете, как можно упростить работу с данными программным способом. 

1. Откройте командную строку, создайте папку git-samples, а затем закройте окно командной строки.

    ```bash
    md "C:\git-samples"
    ```

2. Откройте окно терминала git, например git bash, и выполните команду `cd`, чтобы перейти в новую папку для установки примера приложения.

    ```bash
    cd "C:\git-samples"
    ```

3. Выполните команду ниже, чтобы клонировать репозиторий с примером. Эта команда создает копию примера приложения на локальном компьютере.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. Затем откройте файл решения в Visual Studio.

5. Восстановите пакеты NuGet в проекте, в том числе драйвер Gremlin.Net и пакет Newtonsoft.Json.


6. Вы также можете установить драйвер Gremlin.Net вручную, используя диспетчер пакетов NuGet или [служебную программу командной строки NuGet](https://docs.microsoft.com/nuget/install-nuget-client-tools). 

    ```bash
    nuget install Gremlin.Net
    ```

## <a name="review-the-code"></a>Просмотр кода

Этот шаг является необязательным. Если вы хотите узнать, как создать в коде ресурсы базы данных, изучите приведенные ниже фрагменты кода. Если вас это не интересует, можете сразу переходить к разделу [Обновление строки подключения](#update-your-connection-string). 

Приведенные ниже фрагменты кода взяты из файла Program.cs.

* Задайте параметры подключения на основе созданной выше учетной записи: 

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="configureConnectivity":::

* Команды Gremlin, которые будут выполнены, перечислены в словаре:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineQueries":::

* Создайте новый `GremlinServer` и `GremlinClient` объекты подключения с помощью указанных выше параметров:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineClientandServerObjects":::

* Выполните каждый запрос Gremlin с помощью объекта `GremlinClient` и асинхронной задачи. Вы можете читать запросы Gremlin из словаря, определенного на предыдущем шаге, и выполнять их. Позже получите результат и прочтите значения, которые форматируются в виде словаря, используя класс `JsonSerializer` из пакета Newtonsoft. JSON:

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="executeQueries":::

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение.

1. На [портале Azure](https://portal.azure.com/) перейдите к учетной записи службы базы данных графа. На вкладке **Обзор** вкладку вы увидите две конечные точки: 
 
   **URI пакета SDK для .NET** — это значение используется при подключении к учетной записи графа с помощью библиотеки Microsoft.Azure.Graphs. 

   **Конечная точка Gremlin** — это значение используется при подключении к учетной записи графа с помощью библиотеки Gremlin.Net.

    ![Копирование конечной точки](./media/create-graph-dotnet/endpoint.png)

   Чтобы запустить этот пример, скопируйте значение **конечной точки Gremlin** , удалите номер порта в конце, т. е. URI становится `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com`. Значение конечной точки должно выглядеть `testgraphacct.gremlin.cosmosdb.azure.com`

1. Затем перейдите на вкладку **ключи** и скопируйте значение **первичного ключа** из портал Azure. 

1. После копирования универсального кода ресурса (URI) и ПЕРВИЧного ключа учетной записи сохраните их в новой переменной среды на локальном компьютере, на котором выполняется приложение. Чтобы задать переменную среды, откройте окно командной строки и выполните следующую команду. Обязательно замените значения < Your_Azure_Cosmos_account_URI > и < Your_Azure_Cosmos_account_PRIMARY_KEY >.

   ```console
   setx EndpointUrl "https://<your cosmos db account name>.gremlin.cosmosdb.azure.com"
   setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
   ```

1. Откройте файл *Program.CS* и обновите переменные "Database" и "Container", используя базу данных и контейнер (которые также являются именами графов), созданными выше.

    `private static string database = "your-database-name";` `private static string container = "your-container-or-graph-name";`

1. Сохраните файл Program.cs. 

Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Azure Cosmos DB, обновлено. 

## <a name="run-the-console-app"></a>Запуск консольного приложения

Нажмите клавиши CTRL+F5 для запуска приложения. Приложение выведет команды запроса Gremlin и результаты в консоль.

   В окне консоли появятся вершины и ребра, добавляемые в граф. После завершения сценария закройте окно консоли, нажав клавишу ВВОД.

## <a name="browse-using-the-data-explorer"></a>Просмотр с помощью обозревателя данных

Теперь вернитесь в обозреватель данных на портале Azure. Здесь вы можете просмотреть и запросить новые данные графа.

1. В обозревателе данных новая база данных отображается в области "Графы". Разверните базу данных и узлы контейнера, а затем щелкните **Граф**.

2. Нажмите кнопку **Применить фильтр**, чтобы использовать запрос по умолчанию для просмотра всех вершин графа. Данные, созданные в примере приложения, отображаются на панели Graphs (Графы).

    Вы можете масштабировать граф, развернуть отображаемое пространство, указать дополнительные вершины и переместить их на поверхность отображения.

    ![Просмотр графа в обозревателе данных на портале Azure](./media/create-graph-dotnet/graph-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Следующие шаги

В этом кратком руководстве вы узнали, как создать учетную запись Azure Cosmos DB, граф с помощью обозревателя данных, а также как запустить приложение. Теперь вы можете создавать более сложные запросы и внедрять эффективную логику обхода графа с помощью Gremlin. 

> [!div class="nextstepaction"]
> [Как выполнять запросы к данным в базе данных Azure Cosmos DB с помощью API Graph (предварительная версия)](tutorial-query-graph.md)

