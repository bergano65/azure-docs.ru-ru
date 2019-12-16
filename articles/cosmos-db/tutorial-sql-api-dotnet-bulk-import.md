---
title: Массовый импорт данных в учетную запись API SQL Azure Cosmos DB с помощью пакета SDK для .NET
description: Узнайте, как импортировать или принимать данные в Azure Cosmos DB, создав консольное приложение .NET, которое оптимизирует требуемую подготовленную пропускную способность (ЕЗ/с)
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 79771e082a4a6ffae15f33f636b0300e93bcdaba
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896277"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>Массовый импорт данных в учетную запись API SQL Azure Cosmos DB с помощью пакета SDK для .NET

В этом руководстве описывается, как создать консольное приложение .NET, которое оптимизирует подготовленную пропускную способность (ЕЗ/с), необходимую для импорта данных в Azure Cosmos DB. В этом руководстве вы будете считывать данные из примера источника данных и импортировать их в контейнер Azure Cosmos.
Для работы с этим руководством используется [версия 3.0 или более поздняя версия](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) пакета SDK .NET для Azure Cosmos DB, предназначенного для .NET Framework или .NET Core.

Темы, рассматриваемые в этом руководстве:

> [!div class="checklist"]
> * Создание учетной записи Azure Cosmos.
> * Настройка проекта.
> * Подключение к учетной записи Azure Cosmos с включенной поддержкой массовых операций.
> * Импорт данных с помощью параллельных операций создания.

## <a name="prerequisites"></a>Предварительные требования

Перед выполнением инструкций, приведенных в этой статье, подготовьте следующие ресурсы:

* Активная учетная запись Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Пакет SDK для .NET Core 3](https://dotnet.microsoft.com/download/dotnet-core). Чтобы проверить, какая версия доступна в вашей среде, выполните `dotnet --version`.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Шаг 1. создание учетной записи Azure Cosmos DB;

[Создайте учетную запись API SQL Azure Cosmos DB](create-cosmosdb-resources-portal.md) на портале Azure или с помощью [эмулятора Azure Cosmos DB](local-emulator.md).

## <a name="step-2-set-up-your-net-project"></a>Шаг 2. Настройка проекта .NET

Откройте командную строку Windows или окно терминала с локального компьютера. Все команды в следующих разделах вы будете запускать из командной строки или терминала. Выполните следующую команду dotnet new, чтобы создать приложение с именем *bulk-import-demo*. Параметр `--langVersion` задает свойство *LangVersion* в созданном файле проекта.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

Измените каталог на созданную папку приложения. Чтобы создать приложение, выполните следующую команду:

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

Ожидаемые выходные данные сборки будут выглядеть примерно следующим образом:

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>Шаг 3. Добавление пакета Azure Cosmos DB

Оставаясь в каталоге приложения, установите клиентскую библиотеку Azure Cosmos DB для .NET Core с помощью команды.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>Шаг 4. Получение данных учетной записи Azure Cosmos

Чтобы использовать пример приложения, нужно выполнить проверку подлинности доступа к вашей учетной записи хранения Azure Cosmos. Для проверки подлинности необходимо передать учетную запись Azure Cosmos в приложение. Чтобы просмотреть учетные данные учетной записи Azure Cosmos, сделайте следующее:

1.  Войдите на [портале Azure](https://portal.azure.com/).
1.  Перейдите к своей учетной записи Azure Cosmos.
1.  Откройте панель **Ключи** и скопируйте **URI** и **первичный ключ** своей учетной записи.

Если вы используете эмулятор Azure Cosmos DB, получите [учетные данные эмулятора из этой статьи](local-emulator.md#authenticating-requests).

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>Шаг 5. Инициализация объекта CosmosClient с поддержкой массового выполнения

Откройте созданный файл `Program.cs` в редакторе кода. Вы создадите новый экземпляр CosmosClient с включенным массовым выполнением и используете его для выполнения операций с Azure Cosmos DB. 

Начнем с перезаписи метода по умолчанию `Main` и определения глобальных переменных. Эти глобальные переменные включают в себя конечную точку и ключи авторизации, имя базы данных, контейнер, который вы создадите, и количество элементов, которые будут добавляться в массовую операцию. Обязательно замените значения URL-адреса конечной точки и ключей авторизации в соответствии со своей средой. 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int ItemsToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

В метод `Main` добавьте следующий код для инициализации объекта CosmosClient.

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

После включения массового выполнения CosmosClient выполняет внутреннее группирование параллельных операций в отдельные вызовы службы. Это позволяет оптимизировать использование пропускной способности за счет распределения вызовов службы между секциями и последующего назначения отдельных результатов исходным вызывающим сторонам.

Затем можно будет создать контейнер для хранения всех элементов.  Определите `/pk` в качестве ключа секции, подготовленную пропускную способность в 50 000 ЕЗ/с и настраиваемую политику индексации, которая будет исключать все поля для оптимизации пропускной способности записи. Добавьте приведенный ниже код после инструкции инициализации CosmosClient.

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>Шаг 6. Заполнение списка параллельных задач

Чтобы воспользоваться преимуществами массового выполнения, создайте список асинхронных задач на основе источника данных и операций, которые необходимо выполнить, и используйте `Task.WhenAll` для их параллельного выполнения.
Начнем с использования фиктивных данных для создания списка элементов из нашей модели данных. В реальных приложениях элементы поступают из соответствующего источника данных.

Сначала добавьте пакет Bogus в решение с помощью команды dotnet add package.

   ```bash
   dotnet add package Bogus
   ```

Добавьте определение элементов, которые необходимо сохранить. Необходимо определить класс `Item` в файле `Program.cs`.

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

Затем создайте вспомогательную функцию внутри класса `Program`. Эта вспомогательная функция получит число элементов, которые вы определили для добавления, и создаст случайные данные.

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

Считайте элементы и выполните их сериализацию в экземпляры потока с помощью класса `System.Text.Json`. Ввиду характера автоматически созданных данных они сериализуются в потоки. Кроме того, обращаясь к экземплярам элементов напрямую, но преобразовывая их в потоки, можно воспользоваться производительностью потоковых интерфейсов API в CosmosClient. Обычно к данным можно обращаться напрямую, если известен ключ секции. 


Чтобы преобразовать данные в экземпляры потока, в метод `Main` добавьте следующий код сразу после создания контейнера.

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

Далее используйте потоки данных для создания параллельных задач и заполните список задач для добавления элементов в контейнер. Чтобы выполнить эту операцию, добавьте следующий код в класс `Program`.

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

Все эти параллельные операции с точками будут выполняться одновременно (то есть массово), как описано во вводном разделе.

## <a name="step-7-run-the-sample"></a>Шаг 7. Запуск примера

Чтобы запустить пример, можно просто выполнить команду `dotnet`.

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>Получение полного руководства

Если у вас нет времени на выполнение шагов из этого руководства или вы хотите просто скачать примеры кода, это можно сделать на портале [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer).

После клонирования проекта обязательно обновите соответствующие учетные данные в [Program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/master/src/Program.cs#L25).

Пример можно запустить, указав каталог репозитория и используя `dotnet`.

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы выполнили следующие действия:

> [!div class="checklist"]
> * Создание учетной записи Azure Cosmos.
> * Настройка проекта.
> * Подключение к учетной записи Azure Cosmos с включенной поддержкой массовых операций.
> * Импорт данных с помощью параллельных операций создания.

Теперь вы можете перейти к следующему руководству:

> [!div class="nextstepaction"]
>[Выполнение запросов в Azure Cosmos DB с использованием API SQL](tutorial-query-sql-api.md)