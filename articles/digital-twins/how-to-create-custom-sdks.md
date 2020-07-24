---
title: Создание настраиваемых пакетов SDK для Azure Digital двойников с помощью автоотдыха
titleSuffix: Azure Digital Twins
description: Узнайте, как создавать пользовательские пакеты SDK для использования Azure Digital двойников с языками, отличными от C#.
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2e2a7f09ac6ff3be119a07ed0a2162525801ceef
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87061847"
---
# <a name="create-custom-sdks-for-azure-digital-twins-using-autorest"></a>Создание настраиваемых пакетов SDK для Azure Digital двойников с помощью функции автоотдыха

Сейчас единственным опубликованным пакетом SDK для плоскости данных для взаимодействия с API-интерфейсами Azure Digital двойников является для .NET (C#). Дополнительные сведения о пакете SDK для .NET и интерфейсах API см. в статье [*как использовать интерфейсы API и пакеты SDK для цифровых двойников Azure*](how-to-use-apis-sdks.md). Если вы работаете на другом языке, в этой статье будет показано, как создать собственный пакет SDK на выбранном языке с помощью функции автоотдыха.

## <a name="set-up-your-machine"></a>Настройка компьютера

Чтобы создать пакет SDK, потребуется:
* Функция [автоотдыха](https://github.com/Azure/autorest)версии 2.0.4413 (версия 3 сейчас не поддерживается)
* [Node.js](https://nodejs.org) как предварительные требования для автоотдыха
* [Файл Microsoft Azure Digital двойников Swagger (OpenAPI)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/preview/2020-03-01-preview) , *на которыйdigitaltwins.js*, и его сопроводительную папку примеров. Скачайте файл Swagger и его папку примеров на локальный компьютер.

После того как компьютер оснащен всеми данными из приведенного выше списка, вы можете использовать автооставшуюся для создания пакета SDK.

## <a name="create-the-sdk-with-autorest"></a>Создание пакета SDK с помощью автоотдыха 

Если у вас Node.js установлен, можно выполнить следующую команду, чтобы убедиться, что установлена правильная версия "Автоустановка".
```cmd/sh
npm install -g autorest@2.0.4413
```

Чтобы выполнить автооставшуюся в файле Swagger Azure Digital двойников, выполните следующие действия.
1. Скопируйте файл Swagger Azure Digital двойников и соответствующую папку примеров в рабочий каталог.
2. Используйте окно командной строки, чтобы перейти к рабочему каталогу.
3. Выполните команду "автооставшаяся" с помощью следующей команды. Замените `<language>` заполнитель выбранным языком: `--python` , `--java` , `--go` и т. д. (Полный список параметров можно найти в [файле сведений об авторелизе](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=adtApiSwagger.json --<language> --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

В результате вы увидите новую папку с именем *адтапи* в рабочем каталоге. Создаваемые файлы SDK будут иметь пространство имен *адтапи*. Вы по-прежнему будете использовать это пространство имен с помощью остальных примеров использования в этой статье.

Автооставшаяся версия поддерживает широкий спектр генераторов кода языка.

## <a name="add-the-sdk-to-a-visual-studio-project"></a>Добавление пакета SDK в проект Visual Studio

Вы можете включить файлы, созданные функцией автоотдыха, непосредственно в решение .NET. Однако, вероятно, потребуется включить пакет SDK для Digital двойников в несколько отдельных проектов (клиентские приложения, приложения функций Azure и т. д.). По этой причине может оказаться полезным создать отдельный проект (библиотеку классов .NET) из созданных файлов. Затем этот проект библиотеки классов можно включить в несколько решений в качестве ссылки на проект.

В этом разделе приводятся инструкции по созданию пакета SDK в виде библиотеки классов, которая является собственным проектом и может быть включена в другие проекты. Эти действия зависят от **Visual Studio** (вы можете установить последнюю версию отсюда [).](https://visualstudio.microsoft.com/downloads/)

Для этого выполните указанные ниже действия.

1. Создание нового решения Visual Studio для библиотеки классов
2. Использовать *адтапи* в качестве имени проекта
3. В обозревателе решений щелкните правой кнопкой мыши проект *адтапи* созданного решения и выберите *Добавить > существующий элемент...*
4. Найдите папку, в которой был создан пакет SDK, и выберите файлы на корневом уровне.
5. Нажмите кнопку "ОК"
6. Добавьте папку в проект (щелкните проект правой кнопкой мыши в обозреватель решений и выберите *добавить > Новая папка*).
7. Назовите *модели* папок
8. Щелкните правой кнопкой мыши папку *модели* в обозревателе решений и выберите *Добавить > существующий элемент...*
9. Выберите файлы в папке *Models* СОЗДАННОГО пакета SDK и нажмите кнопку ОК.

Для успешного создания пакета SDK в проекте понадобятся следующие ссылки:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Чтобы добавить их, откройте *инструменты > диспетчер пакетов nuget > управления пакетами NuGet для решения.*...

1. На панели убедитесь, что выбрана вкладка *Обзор* .
2. Поиск *Microsoft. RESTful*
3. Выберите `ClientRuntime` пакеты и `ClientRuntime.Azure` и добавьте их в решение.

Теперь можно создать проект и включить его в качестве ссылки на проект в любое написанное вами приложение Azure Digital двойников.

## <a name="general-guidelines-for-generated-sdks"></a>Общие рекомендации для созданных пакетов SDK

В этом разделе содержатся общие сведения о и рекомендациях по использованию созданного пакета SDK.

### <a name="synchronous-and-asynchronous-calls"></a>Синхронные и асинхронные вызовы

Все функции пакета SDK имеют синхронные и асинхронные версии.

### <a name="typed-and-untyped-data"></a>Типизированные и нетипизированные данные

REST API вызовы обычно возвращают строго типизированные объекты. Тем не менее, так как Azure Digital двойников позволяет пользователям определять собственные пользовательские типы для двойников, невозможно предварительно определить статические возвращаемые данные для многих вызовов Azure Digital двойников. Вместо этого интерфейсы API возвращают строго типизированные типы оболочек, где это применимо, а пользовательские данные двойника находятся в объектах Json.NET (используется везде, где в сигнатурах API отображается тип данных Object). Эти объекты можно привести к соответствующим образом в коде.

### <a name="error-handling"></a>Обработка ошибок

При возникновении ошибки в пакете SDK (включая ошибки HTTP, такие как 404) пакет SDK выдает исключение. По этой причине важно инкапсулировать все вызовы API внутри блоков try/catch.

Ниже приведен фрагмент кода, который пытается добавить двойника и перехватывает все ошибки в этом процессе:

```csharp
try
{
    await client.DigitalTwins.AddAsync(id, initData);
    Console.WriteLine($"Created a twin successfully: {id}");
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
}
```

### <a name="paging"></a>Разбивка на страницы

При автоrestful создаются два типа шаблонов подкачки для пакета SDK:
* Один для всех API, за исключением API запросов
* Один для API запроса

В шаблоне постраничного просмотра, не относящемся к запросу, существует две версии каждого вызова:
* Версия для выполнения начального вызова (например, `DigitalTwins.ListEdges()` )
* Версия для получения следующих страниц. Эти вызовы имеют суффикс "Next" (например, `DigitalTwins.ListEdgesNext()` )

Ниже приведен фрагмент кода, демонстрирующий получение выгружаемого списка исходящих отношений из Azure Digital двойников:
```csharp
try
{
    // List to hold the results in
    List<object> relList = new List<object>();
    // Enumerate the IPage object returned to get the results
    // ListAsync will throw if an error occurs
    IPage<object> relPage = await client.DigitalTwins.ListEdgesAsync(id);
    relList.AddRange(relPage);
    // If there are more pages, the NextPageLink in the page is set
    while (relPage.NextPageLink != null)
    {
        // Get more pages...
        relPage = await client.DigitalTwins.ListEdgesNextAsync(relPage.NextPageLink);
        relList.AddRange(relPage);
    }
    Console.WriteLine($"Found {relList.Count} relationships on {id}");
    // Do something with each object found
    // As relationships are custom types, they are JSON.Net types
    foreach (JObject r in relList)
    {
        string relId = r.Value<string>("$edgeId");
        string relName = r.Value<string>("$relationship");
        Console.WriteLine($"Found relationship {relId} from {id}");
    }
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error retrieving relationships on {id}: {e.Response.StatusCode}");
}
```

Второй шаблон создается только для API запроса. Он явно используется `continuationToken` .

Ниже приведен пример с этим шаблоном:

```csharp
string query = "SELECT * FROM digitaltwins";
string conToken = null; // continuation token from the query
int page = 0;
try
{
    // Repeat the query while there are pages
    do
    {
        QuerySpecification spec = new QuerySpecification(query, conToken);
        QueryResult qr = await client.Query.QueryTwinsAsync(spec);
        page++;
        Console.WriteLine($"== Query results page {page}:");
        if (qr.Items != null)
        {
            // Query returns are JObjects
            foreach(JObject o in qr.Items)
            {
                string twinId = o.Value<string>("$dtId");
                Console.WriteLine($"  Found {twinId}");
            }
        }
        Console.WriteLine($"== End query results page {page}");
        conToken = qr.ContinuationToken;
    } while (conToken != null);
} catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error in twin query: ${e.Response.StatusCode}");
}
```

## <a name="next-steps"></a>Дальнейшие действия

Пошаговые инструкции по созданию клиентского приложения, в котором можно использовать пакет SDK:
* [*Руководство. Написание кода для клиентского приложения*](tutorial-code.md)