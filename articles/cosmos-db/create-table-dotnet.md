---
title: Краткое руководство. Использование API таблиц с .NET в Azure Cosmos DB
description: В этом руководстве показано, как использовать API таблицы Azure Cosmos DB для создания приложения с помощью портала Azure и .NET
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 7ca51b176c17f33b4779a0129c5dc57b220c0097
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57877638"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Краткое руководство. Создание приложения API таблиц с помощью пакета SDK .NET и Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
>  

В этом руководстве показано, как использовать .NET и [API таблицы](table-introduction.md) Azure Cosmos DB для создания приложения путем клонирования примера с сайта GitHub. Кроме того, здесь показано, как создать учетную запись Azure Cosmos DB и использовать обозреватель данных для создания таблиц и сущностей на веб-портале Azure.

## <a name="prerequisites"></a>Предварительные требования

Если вы еще не установили Visual Studio 2017, вы можете скачать и использовать **бесплатный** [выпуск Community для Visual Studio 2017](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность **разработки для Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Добавление таблицы

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Добавление демонстрационных данных

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь необходимо клонировать приложение "Таблица" из GitHub. Задайте строку подключения и выполните ее. Вы узнаете, как можно упростить работу с данными программным способом. 

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
   git clone https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started.git
   ```
   > [!NOTE]
   > Стандартный образец .NET, который описан в этой документации, работает как с API таблиц Azure Cosmos DB, так и с хранилищем таблиц Azure. Если вы хотите запустить образец, который работает на .NET Framework 4.5, см. пример [storage-table-dotnet-getting-started](https://github.com/Azure-Samples/storage-table-dotnet-getting-started). 


## <a name="open-the-sample-application-in-visual-studio"></a>Открытие примера приложения в Visual Studio

1. В Visual Studio в меню **Файл** нажмите **Открыть** и выберите **проект или решение**. 

   ![Открытие решения](media/create-table-dotnet/azure-cosmosdb-open-solution.png) 

2. Перейдите к папке, в которую вы клонировали пример приложения, и откройте файл TableStorage.sln.

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение. Так вы обеспечите обмен данными между приложением и размещенной базой данных. 

1. На [портале Azure](https://portal.azure.com/) щелкните **Строка подключения**. Используйте кнопку копирования в правой части окна, чтобы скопировать **основную строку подключения**.

   ![Просмотр и копирование основной строки подключения на панели строки подключения](./media/create-table-dotnet/connection-string.png)

2. Откройте файл **Settings.json** в Visual Studio. 

3. Вставьте значение **Первичная строка подключения**, полученное на портале, вместо значения параметра StorageConnectionString. Значение нужно вставлять в кавычках.

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```
   При использовании образца [.NET Framework](https://github.com/Azure-Samples/storage-table-dotnet-getting-started) необходимо выполнить обновление строки подключения, которая находится в файле **App.config**.

4. Чтобы сохранить файл **Settings.json**, нажмите CTRL+S.

Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Azure Cosmos DB, обновлено. 

## <a name="build-and-deploy-the-app"></a>Создание и развертывание приложения

1. Правой кнопкой мыши щелкните проект **CosmosTableSamples** в **обозревателе решений** Visual Studio и выберите **Управление пакетами NuGet**. 

   ![Управление пакетами NuGet](media/create-table-dotnet/azure-cosmosdb-manage-nuget.png)

2. В поле **Обзор** NuGet введите Microsoft.Azure.Cosmos.Table. Это запрос для поиска клиентской библиотеки API таблицы Cosmos DB. Обратите внимание, что на данный момент эта библиотека доступна только для .NET Framework и .NET Standard. 

   > [!NOTE]
   > При использовании образцов, для которых требуется NET Framework, необходимо установить пакет NuGet *Microsoft.Azure.CosmosDB.Table*. При использовании образца .NET Framework с хранилищем таблиц Azure, кроме пакета NuGet для Cosmos DB, вам также потребуется пакет NuGet *Microsoft.Azure.Storage.Common*. 
   
   ![Вкладка "Обзор" NuGet](media/create-table-dotnet/azure-cosmosdb-nuget-browse.png)

3. Щелкните **Установить**, чтобы установить библиотеку **Microsoft.Azure.Cosmos.Table**. Установится пакет API таблицы Azure Cosmos DB и все зависимые компоненты.

4. При выполнении всего приложения данные образца вставляются в сущность таблицы и удаляются в конце выполнения. Поэтому вы не увидите вставленные данные, если выполните образец целиком. Тем не менее, чтобы просмотреть данные, вы можете вставить некоторые точки останова. Откройте файл BasicSamples.cs и щелкните правой кнопкой мыши строку 52, нажмите **Точка останова** и выберите **Вставить точку останова**. Вставьте еще одну точку останова в строке 55.

   ![Добавление точки останова](media/create-table-dotnet/azure-cosmosdb-breakpoint.png) 

5. Нажмите клавишу F5 для запуска приложения. В окне консоли отобразится имя новой базы данных таблиц (в этом примере — demoa13b1) в Azure Cosmos DB. 
    
   ![Вывод на консоль](media/create-table-dotnet/azure-cosmosdb-console.png)

   Когда будет достигнута первая точка останова, вернитесь к обозревателю данных на портале Azure. Нажмите кнопку **Обновить**, разверните пример* таблицы и щелкните **Сущности**. На вкладке **Сущности** справа отобразится новая сущность, добавленная для Walter Harp. Обратите внимание, что для новой сущности указан номер телефона 425-555-0101.

   ![Новая сущность](media/create-table-dotnet/azure-cosmosdb-entity.png)
    
   Если отобразится сообщение об ошибке "При запуске проекта файл Settings.json не удалось найти", то ее можно устранить путем добавления в настройки проекта следующей XML-записи. Правой кнопкой мыши щелкните CosmosTableSamples, выберите "Редактировать CosmosTableSamples.csproj", а затем добавьте следующую группу itemGroup. 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. Закройте вкладку **Сущности** в обозревателе данных.
    
7. Нажмите клавишу F5, чтобы приложение выполнялось до следующей точки останова. 

    Когда будет достигнута точка останова, снова щелкните **Сущности** на портале Azure, чтобы открыть вкладку **Сущности**. Обратите внимание, что номер телефона изменился на 425-555-0105.

8. Нажмите клавишу F5, чтобы запустить приложение. 
 
   Приложение добавляет сущности для использования в расширенном примере приложения, который API таблицы сейчас не поддерживает. Затем приложение удаляет таблицу, созданную в примере приложения.

9. Откройте окно консоли и нажмите клавишу ВВОД, чтобы завершить работу приложения. 
  

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как создать учетную запись Azure Cosmos DB и таблицу с помощью обозревателя данных, а также как запустить приложение.  Теперь вы можете выполнить запрос данных с помощью API таблиц.  

> [!div class="nextstepaction"]
> [Импорт данных таблиц в API таблицы](table-import.md)

