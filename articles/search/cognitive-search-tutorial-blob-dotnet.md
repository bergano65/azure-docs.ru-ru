---
title: Учебник. C# и AI через BLOB-объекты Azure
titleSuffix: Azure Cognitive Search
description: Пошаговое описание примера извлечения текста и обработки естественного языка по содержимому в хранилище C# BLOB-объектов с помощью и пакета SDK Azure когнитивный Поиск .NET.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 85fb709dfcca45b6ca8141c6d3de1941044f5ee5
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163206"
---
# <a name="tutorial-use-c-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Учебник. Использование C# и AI для создания содержимого с возможностью поиска из больших двоичных объектов Azure

При наличии неструктурированного текста или изображений в хранилище BLOB-объектов Azure [конвейер обогащения искусственного интеллекта](cognitive-search-concept-intro.md) может извлекать информацию и создавать новое содержимое, которое полезно для полнотекстового поиска или сценариев интеллектуального анализа знаний. В этом C# руководстве для создания новых полей, которые можно использовать в запросах, аспектах и фильтрах, применяется оптическое распознавание символов (OCR) на изображениях и выполняется обработка на естественном языке.

В этом руководстве для выполнения C# следующих задач используйте и [пакет SDK для .NET](https://aka.ms/search-sdk) .

> [!div class="checklist"]
> * Начните с файлов приложений и образов в хранилище BLOB-объектов Azure.
> * Определите конвейер для добавления OCR, извлечения текста, определения языка, сущности и распознавания ключевых фраз.
> * Определите индекс для хранения выходных данных (необработанное содержимое и созданные конвейером пары "имя — значение").
> * Выполните конвейер, чтобы начать преобразования и анализ, а также создать и загрузить индекс.
> * Изучите результаты с помощью полнотекстового поиска и расширенного синтаксиса запросов.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

+ [Хранилище Azure](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Создание](search-create-service-portal.md) или [Поиск существующей службы поиска](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Для работы с этим руководством можно использовать бесплатную службу. Бесплатная служба поиска ограничивает вас тремя индексами, тремя индексаторами и тремя источниками данных. В этом руководстве создается по одному объекту из каждой категории. Прежде чем начать, убедитесь, что в службе есть место для принятия новых ресурсов.

## <a name="download-files"></a>Загрузка файлов

1. Откройте эту [папку OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) и щелкните **Скачать** вверху слева, чтобы скопировать файлы на локальный компьютер. 

1. Щелкните ZIP-файл правой кнопкой мыши и выберите **Извлечь все**. Доступно 14 файлов разных типов. Используйте их для работы с этим руководством.

## <a name="1---create-services"></a>1\. Создание служб

В этом руководстве используется Когнитивный поиск Azure для индексирования и запросов, Cognitive Services в серверной части для обогащения искусственного интеллекта и хранилища BLOB-объектов Azure для предоставления данных. В этом учебнике остается свободное выделение 20 транзакций на индексатор в день в Cognitive Services, поэтому необходимо создать только службы поиска и хранения.

Если это возможно, создайте оба в одном регионе и группе ресурсов для сходства и управляемости. В реальной системе учетная запись хранения Azure может находиться в любом регионе.

### <a name="start-with-azure-storage"></a>Служба хранилища Azure

1. [Войдите на портал Azure](https://portal.azure.com/) и щелкните **+ Создать ресурс**.

1. Выполните поиск по строке *учетная запись хранения* и выберите "Учетная запись хранения Microsoft".

   ![Создание учетной записи хранения](media/cognitive-search-tutorial-blob/storage-account.png "Создание учетной записи хранения")

1. На вкладке "Основные сведения" нужно ввести следующие сведения. Сохраните значения по умолчанию для остальных параметров.

   + **Группа ресурсов**. Вы можете выбрать существующую группу ресурсов или создать новую, но для всех служб это должна быть одна и та же группа для совместного управления.

   + **Имя учетной записи хранения.** Если вы ожидаете, что у вас будет несколько ресурсов одного типа, используйте имена, которые будут четко определять тип и регион, например *blobstoragewestus*. 

   + **Расположение.** Желательно выбрать то же расположение, которое использовалось для Когнитивного поиска Azure и Cognitive Services. При использовании одного расположения плата за трафик не взимается.

   + **Тип учетной записи.** Выберите значение по умолчанию *StorageV2 (общего назначения версии 2)* .

1. Щелкните **Просмотреть и создать**, чтобы создать службу.

1. По завершении процесса щелкните **Перейти к ресурсу** чтобы открыть страницу со сводкой.

1. Щелкните службу **Большие двоичные объекты**.

1. Щелкните **+ контейнер** , чтобы создать контейнер и присвойте ему имя *Basic-Demo-Data-PR*.

1. Выберите *базовый-Demo-Data-PR* , а затем нажмите кнопку **Отправить** , чтобы открыть папку, в которую были сохранены файлы для загрузки. Выберите все файлы четырнадцать и нажмите кнопку **ОК** для отправки.

   ![Отправка образцов файлов](media/cognitive-search-quickstart-blob/sample-data.png "Отправка образцов файлов")

1. Прежде чем выйти из службы хранилища Azure, получите строку подключения для создания подключения в Когнитивном поиске Azure. 

   1. Вернитесь на страницу со сводкой об учетной записи хранения (в качестве примера мы использовали *blobstragewestus*). 
   
   1. В области навигации слева выберите **Ключи доступа** и скопируйте одну из строк подключения. 

   Строка подключения — это URL-адрес в следующем формате:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Сохраните эту строку подключения в Блокноте. Она понадобится позже при настройке подключения к источнику данных.

### <a name="cognitive-services"></a>Cognitive Services

Обогащение ИИ основано на платформе Cognitive Services, которая включает службы "Анализ текста" для обработки естественного языка и "Компьютерное зрение" для обработки изображений. Если бы вы создавали реальный прототип или проект, на этом этапе нужно было бы создать Cognitive Services (в том же регионе, что и Когнитивный поиск Azure) для связывания с операциями индексирования.

Но для нашего примера подготовку ресурсов можно пропустить, так как Когнитивный поиск Azure может подключаться к Cognitive Services в фоновом режиме и предоставляет 20 бесплатных транзакций для каждого выполнения индексатора. В примере из этого руководства используется всего семь транзакций. Это значит, что нам достаточно уровня "Бесплатный". Для крупных проектов вам, скорее всего, нужно будет подготовить Cognitive Services на уровне S0 с оплатой по мере использования. См. сведения о [подключении Cognitive Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Когнитивный поиск Azure

Третий компонент — Когнитивный поиск Azure, который вы можете [создать на портале](search-create-service-portal.md). Для выполнения действий в этом пошаговом руководстве можно использовать уровень "Бесплатный". 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Получение ключа API и URL-адреса конечной точки для администрирования Когнитивного поиска Azure

Для взаимодействия со службой "Когнитивный поиск Azure" вам нужны URL-адрес службы и ключ доступа. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Когнитивный поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **обзора** службы поиска получите URL-адрес. Пример конечной точки может выглядеть так: `https://mydemo.search.windows.net`.

1. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

   Получите также ключ запроса. Мы рекомендуем создавать запросы с доступом только для чтения.

   ![Получение имени службы, ключей запросов и администратора](media/search-get-started-nodejs/service-name-and-keys.png)

Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

## <a name="2---set-up-your-environment"></a>2\. Настройка среды

Сначала откройте Visual Studio и создайте новый проект консольного приложения, которое будет выполняться на базе .NET Core.

### <a name="install-nuget-packages"></a>Установка пакетов Nuget

[Пакет SDK службы "Когнитивный поиск Azure" для .NET](https://aka.ms/search-sdk) содержит несколько клиентских библиотек, которые позволяют управлять индексами, источниками данных, индексаторами и наборами навыков, а также отправлять документы, управлять ими и выполнять запросы, не вникая в детали HTTP и JSON. Эти клиентские библиотеки распределяются как пакеты NuGet.

Для этого проекта установите версию 9 или более позднюю версию `Microsoft.Azure.Search` пакета NuGet.

1. Откройте консоль диспетчера пакетов. Выберите **Инструменты** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**. 

1. Перейдите на [страницу пакета NuGet Microsoft. Azure. Search](https://www.nuget.org/packages/Microsoft.Azure.Search).

1. Выберите последнюю версию (9 или более позднюю).

1. Скопируйте команду диспетчера пакетов.

1. Вернитесь в консоль диспетчера пакетов и выполните команду, скопированную на предыдущем шаге.

Затем установите последнюю версию пакета NuGet `Microsoft.Extensions.Configuration.Json`.

1. Выберите **инструменты** > **диспетчер пакетов NuGet** > **Управление пакетами NuGet для решения.** ... 

1. Нажмите кнопку **Обзор** и найдите `Microsoft.Extensions.Configuration.Json` пакет NuGet. 

1. Выберите пакет, выберите свой проект, убедитесь, что используется последняя стабильная версия, а затем нажмите кнопку **установить**.

### <a name="add-service-connection-information"></a>Добавление сведений о подключении к службе

1. Щелкните правой кнопкой мыши проект в обозреватель решений и выберите **добавить** > **новый элемент...** . 

1. Присвойте файлу имя `appsettings.json` и нажмите **Добавить**. 

1. Включите этот файл в выходной каталог.
    1. Щелкните правой кнопкой мыши `appsettings.json` и выберите пункт **Свойства**. 
    1. Измените значение параметра **Копировать в выходной каталог** , чтобы **Копировать, если новее**.

1. Скопируйте указанный ниже файл JSON в новый файл JSON.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Укажите службу поиска и добавьте сведения об учетной записи хранения BLOB-объектов. Помните, что эти сведения можно получить из шагов подготовки службы, указанных в предыдущем разделе.

### <a name="add-namespaces"></a>Добавление пространств имен

В `Program.cs`добавьте следующие пространства имен.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Создание клиента

Создайте экземпляр класса `SearchServiceClient` в разделе main.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` создает экземпляр `SearchServiceClient`, используя значения, которые хранятся в файле конфигурации приложения (appsettings.json).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> Класс `SearchServiceClient` управляет подключениями к службе поиска. Во избежание открытия слишком большого числа подключений в приложении рекомендуется по возможности совместно использовать один экземпляр `SearchServiceClient` . Его методы поддерживают такое использование, так как являются потокобезопасными.
> 

## <a name="3---create-the-pipeline"></a>3\. Создание конвейера

В Когнитивном поиске Azure обработка ИИ выполняется во время индексирования (или приема данных). В этой части пошагового руководства описано, как создать четыре объекта: источник данных, определение индекса, набор навыков и индексатор. 

### <a name="step-1-create-a-data-source"></a>Шаг 1. Создание источника данных

У класса `SearchServiceClient` есть свойство `DataSources`. Это свойство предоставляет все методы, которые требуются для создания, перечисления, обновления или удаления источников данных Когнитивного поиска Azure.

Вызовите метод `DataSource`, чтобы создать экземпляр `serviceClient.DataSources.CreateOrUpdate(dataSource)`. При использовании метода `DataSource.AzureBlobStorage` нужно указать имя источника данных, строку подключения и имя контейнера больших двоичных объектов.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "basic-demo-data-pr",
        description: "Demo files to demonstrate cognitive search capabilities.");

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.DataSources.CreateOrUpdate(dataSource);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

При успешном выполнении запроса метод вернет сведения о созданном источнике данных. Если при выполнении запроса возникнет ошибка, например из-за недопустимого параметра, метод вернет исключение.

Теперь добавьте строку в Main, чтобы вызвать только что добавленную функцию `CreateOrUpdateDataSource`.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    // Create or Update the data source
    Console.WriteLine("Creating or updating the data source...");
    DataSource dataSource = CreateOrUpdateDataSource(serviceClient, configuration);
```


<!-- 
```csharp
DataSource dataSource = DataSource.AzureBlobStorage(
    name: "demodata",
    storageConnectionString: configuration["AzureBlobConnectionString"],
    containerName: "basic-demo-data-pr",
    deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
        softDeleteColumnName: "IsDeleted",
        softDeleteMarkerValue: "true"),
    description: "Demo files to demonstrate cognitive search capabilities.");
```

Now that you have initialized the `DataSource` object, create the data source. `SearchServiceClient` has a `DataSources` property. This property provides all the methods you need to create, list, update, or delete Azure Cognitive Search data sources.

For a successful request, the method will return the data source that was created. If there is a problem with the request, such as an invalid parameter, the method will throw an exception.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
``` -->

Выполните сборку и запуск решения. Так как это ваш первый запрос, на портале Azure убедитесь, что источник данных был создан в службе "Когнитивный поиск Azure". На странице панели мониторинга службы поиска проверьте наличие нового элемента на плитке "Источники данных". Возможно, вам придется подождать несколько минут, пока обновится страница портала.

  ![Плитка источников данных на портале](./media/cognitive-search-tutorial-blob/data-source-tile.png "Плитка источников данных на портале")

### <a name="step-2-create-a-skillset"></a>Шаг 2. Создание набора навыков

В рамках этого раздела вы определите набор требуемых шагов обогащения данных. Каждый шаг обогащения называется *навыком*, а набор шагов обогащения — *набором навыков*. В этом руководстве для набора навыков используются [встроенные когнитивные навыки](cognitive-search-predefined-skills.md):

+ [Оптическое распознавание символов](cognitive-search-skill-ocr.md) для распознавания печатного и рукописного текста в файлах изображений.

+ [Объединение текста](cognitive-search-skill-textmerger.md) для объединения текста из коллекции полей в одном поле.

+ [Распознавание языка](cognitive-search-skill-language-detection.md) для определения языка содержимого.

+ [Разделение текста](cognitive-search-skill-textsplit.md) для разбиения объемного содержимого на более мелкие фрагменты перед вызовом навыка извлечения ключевых фраз и навыка распознавания сущностей. Функции извлечения ключевых фраз и распознавания сущностей принимают входные данные объемом не более 50 000 символов. Некоторые примеры файлов следует разделить, чтобы удовлетворить это ограничение.

+ [Распознавание сущностей](cognitive-search-skill-entity-recognition.md) для извлечения названий организаций из содержимого в контейнере больших двоичных объектов.

+ [Извлечение ключевых фраз](cognitive-search-skill-keyphrases.md) для получения основных ключевых фраз.

В ходе первоначальной обработки Когнитивный поиск Azure открывает каждый документ для считывания содержимого из разных форматов файлов. Найденный текст в исходном файле помещается в созданное поле ```content```, по одному для каждого документа. Таким образом, задайте входные данные как ```"/document/content"```, чтобы использовать этот текст. 

Выходные данные могут быть сопоставлены с индексом, используемым в качестве входных данных для нисходящего навыка, или к обоим, как в случае с кодом языка. В индексе код языка полезен для фильтрации. В качестве входных данных код языка используется навыками анализа текста, чтобы указать лингвистические правила для разбивки слов.

Общие сведения о наборах навыков см. в статье [How to create a skillset in an enrichment pipeline](cognitive-search-defining-skillset.md) (Способ создания набора навыков в конвейере обогащения).

### <a name="ocr-skill"></a>Навык OCR

Навык **оптического распознавания текста** позволяет извлечь текст из изображений. При использовании этого навыка предполагается наличие поля normalized_images. Чтобы создать это поле, далее в руководстве мы зададим для конфигурации ```"imageAction"``` в определении индексатора значение ```"generateNormalizedImages"```.

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "image",
        source: "/document/normalized_images/*"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "text",
        targetName: "text"));

    OcrSkill ocrSkill = new OcrSkill(
        description: "Extract text (plain and structured) from image",
        context: "/document/normalized_images/*",
        inputs: inputMappings,
        outputs: outputMappings,
        defaultLanguageCode: OcrSkillLanguage.En,
        shouldDetectOrientation: true);

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Навык объединения

В рамках этого раздела вы создадите навык **объединения**, который позволяет объединить поле содержимого документа с текстом, полученным с помощью навыка оптического распознавания текста.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/content"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "itemsToInsert",
        source: "/document/normalized_images/*/text"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "offsets",
        source: "/document/normalized_images/*/contentOffset"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "mergedText",
        targetName: "merged_text"));

    MergeSkill mergeSkill = new MergeSkill(
        description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        insertPreTag: " ",
        insertPostTag: " ");

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Навык распознавания языка

Навык **распознавания языка** определяет язык введенного текста и сообщает один код языка для каждого документа, отправленного по запросу. Мы будем использовать выходные данные навыка **распознавания языка** в качестве части входных данных для навыка **разделения текста**.

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "languageCode",
        targetName: "languageCode"));

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
        description: "Detect the language used in the document",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings);

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Навык разделения текста

Приведенный ниже код навыка **разделения текста** позволяет разделить текст на страницы и устанавливает для них ограничение в 4000 символов, измеренных методом `String.Length`. С помощью алгоритма текст будет при возможности разделен фрагменты, размер которых не превышает значение `maximumPageLength`. В этом случае будет сделано все возможное, чтобы разделить предложение на его границе. Поэтому размер фрагмента будет немного меньше, чем значение `maximumPageLength`.

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();

    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "textItems",
        targetName: "pages"));

    SplitSkill splitSkill = new SplitSkill(
        description: "Split content into pages",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        textSplitMode: TextSplitMode.Pages,
        maximumPageLength: 4000);

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Навык распознавания сущностей

Этот экземпляр `EntityRecognitionSkill` настроен для распознавания типа категории `organization`. Навык **распознавания сущностей** также может распознать типы категорий `person` и `location`.

Обратите внимание, что в поле context задается значение ```"/document/pages/*"``` со звездочкой. Это означает, что этап обогащения вызывается для каждой страницы в ```"/document/pages"```.

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "organizations",
        targetName: "organizations"));

    List<EntityCategory> entityCategory = new List<EntityCategory>();
    entityCategory.Add(EntityCategory.Organization);

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
        description: "Recognize organizations",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings,
        categories: entityCategory,
        defaultLanguageCode: EntityRecognitionSkillLanguage.En);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Навык извлечения ключевых фраз

Как и созданный только что экземпляр `EntityRecognitionSkill`, навык **извлечения ключевых фраз** вызывается для каждой страницы документа.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "keyPhrases",
        targetName: "keyPhrases"));

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
        description: "Extract the key phrases",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings);

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>Сборка и создание наборов навыков

Выполните сборку `Skillset` с использованием созданных вами навыков.

```csharp
private static Skillset CreateOrUpdateDemoSkillSet(SearchServiceClient serviceClient, IList<Skill> skills)
{
    Skillset skillset = new Skillset(
        name: "demoskillset",
        description: "Demo skillset",
        skills: skills);

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.Skillsets.CreateOrUpdate(skillset);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Добавьте следующие строки в Main.

```csharp
    // Create the skills
    Console.WriteLine("Creating the skills...");
    OcrSkill ocrSkill = CreateOcrSkill();
    MergeSkill mergeSkill = CreateMergeSkill();
    EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
    LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
    SplitSkill splitSkill = CreateSplitSkill();
    KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

    // Create the skillset
    Console.WriteLine("Creating or updating the skillset...");
    List<Skill> skills = new List<Skill>();
    skills.Add(ocrSkill);
    skills.Add(mergeSkill);
    skills.Add(languageDetectionSkill);
    skills.Add(splitSkill);
    skills.Add(entityRecognitionSkill);
    skills.Add(keyPhraseExtractionSkill);

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>Шаг 3. Создание индекса

В этом разделе вы определите схему индекса, указав поля для включения в индекс поиска, а также атрибуты поиска для каждого поля. Поля имеют тип и могут принимать атрибуты, определяющие их использование (поиск, сортировка и т. д.). Имена полей в индексе не обязательно должны точно соответствовать именам полей в источнике. На более позднем этапе вы добавите сопоставления полей в индексаторе для подключения полей "источник — назначение". Для этого шага определите индекс, используя соглашения об именовании полей, относящиеся к вашему поисковому приложению.

В этом упражнении используются следующие поля и типы полей:

| имена полей: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| типы полей: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


#### <a name="create-demoindex-class"></a>Создание класса DemoIndex

Поля для этого индекса определяются с помощью класса модели. Каждое свойство класса модели имеет атрибуты, которые определяют связанные с поиском характеристики соответствующего поля индекса. 

Мы добавим класс модели в новый файл C#. Щелкните проект правой кнопкой мыши и последовательно выберите **Добавить** > **Новый элемент...** . Выберите "Класс", присвойте файлу имя `DemoIndex.cs`, а затем щелкните **Добавить**.

Обязательно укажите, что нужно использовать типы из пространств имен `Microsoft.Azure.Search` и `Microsoft.Azure.Search.Models`.

Добавьте указанное ниже определение класса модели в файл `DemoIndex.cs` и включите его в то же пространство имен, где будете создавать индекс.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
    // It ensures that Pascal-case property names in the model class are mapped to camel-case
    // field names in the index.
    [SerializePropertyNamesAsCamelCase]
    public class DemoIndex
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsSearchable, IsSortable]
        public string Id { get; set; }

        [IsSearchable]
        public string Content { get; set; }

        [IsSearchable]
        public string LanguageCode { get; set; }

        [IsSearchable]
        public string[] KeyPhrases { get; set; }

        [IsSearchable]
        public string[] Organizations { get; set; }
    }
}
```

<!-- Add the below model class definition to `DemoIndex.cs` and include it in the same namespace where you'll create the index.

```csharp
// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Cognitive Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public class DemoIndex
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsSearchable, IsSortable]
    public string Id { get; set; }

    [IsSearchable]
    public string Content { get; set; }

    [IsSearchable]
    public string LanguageCode { get; set; }

    [IsSearchable]
    public string[] KeyPhrases { get; set; }

    [IsSearchable]
    public string[] Organizations { get; set; }
}
``` -->

Теперь, когда вы определили класс модели, можно легко создать определение индекса в файле `Program.cs`. Имя этого индекса будет `demoindex`. Если индекс с таким именем уже существует, он будет удален.

```csharp
private static Index CreateDemoIndex(SearchServiceClient serviceClient)
{
    var index = new Index()
    {
        Name = "demoindex",
        Fields = FieldBuilder.BuildForType<DemoIndex>()
    };

    try
    {
        bool exists = serviceClient.Indexes.Exists(index.Name);

        if (exists)
        {
            serviceClient.Indexes.Delete(index.Name);
        }

        serviceClient.Indexes.Create(index);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

При тестировании вы можете обнаружить, что пытаетесь создать уже существующий индекс. Поэтому предварительно проверяйте, не создан ли он.

Добавьте следующие строки в Main.

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Index demoIndex = CreateDemoIndex(serviceClient);
```

<!-- ```csharp
try
{
    bool exists = serviceClient.Indexes.Exists(index.Name);

    if (exists)
    {
        serviceClient.Indexes.Delete(index.Name);
    }

    serviceClient.Indexes.Create(index);
}
catch (Exception e)
{
    // Handle exception
}
```
 -->

Дополнительные сведения об определении индекса см. в статье [Create Index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Создание индексов (REST API службы "Поиск Azure")).

### <a name="step-4-create-and-run-an-indexer"></a>Шаг 4. Создание и запуск индексатора

Пока вы создали источник данных, набор навыков и индекс. Эти три компонента становятся частью [индексатора](search-indexer-overview.md), который объединяет каждую часть в единую многофазную операцию. Чтобы связать их вместе в индексаторе, необходимо определить сопоставления полей.

+ Обработка FieldMappings выполняется перед набором навыков. Исходные поля из источника данных сопоставляются с целевыми полями в индексе. Если имена и типы полей одинаковы в исходном и целевом расположениях, сопоставление не требуется.

+ Обработка OutputFieldMappings выполняется после набора навыков. Добавляются ссылки на элементы sourceFieldNames, которые не существуют, пока они не будут созданы при анализе или обогащении документов. TargetFieldName — это поле в индексе.

Помимо привязки входных данных к выходным данным, вы можете использовать сопоставления полей для преобразования структур данных в плоские структуры. Для получения дополнительных сведений см. раздел [Сопоставление обогащенных полей с индексом, поддерживающим поиск](cognitive-search-output-field-mapping.md).

```csharp
private static Indexer CreateDemoIndexer(SearchServiceClient serviceClient, DataSource dataSource, Skillset skillSet, Index index)
{
    IDictionary<string, object> config = new Dictionary<string, object>();
    config.Add(
        key: "dataToExtract",
        value: "contentAndMetadata");
    config.Add(
        key: "imageAction",
        value: "generateNormalizedImages");

    List<FieldMapping> fieldMappings = new List<FieldMapping>();
    fieldMappings.Add(new FieldMapping(
        sourceFieldName: "metadata_storage_path",
        targetFieldName: "id",
        mappingFunction: new FieldMappingFunction(
            name: "base64Encode")));
    fieldMappings.Add(new FieldMapping(
        sourceFieldName: "content",
        targetFieldName: "content"));

    List<FieldMapping> outputMappings = new List<FieldMapping>();
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/pages/*/organizations/*",
        targetFieldName: "organizations"));
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/pages/*/keyPhrases/*",
        targetFieldName: "keyPhrases"));
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/languageCode",
        targetFieldName: "languageCode"));

    Indexer indexer = new Indexer(
        name: "demoindexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        description: "Demo Indexer",
        skillsetName: skillSet.Name,
        parameters: new IndexingParameters(
            maxFailedItems: -1,
            maxFailedItemsPerBatch: -1,
            configuration: config),
        fieldMappings: fieldMappings,
        outputFieldMappings: outputMappings);

    try
    {
        bool exists = serviceClient.Indexers.Exists(indexer.Name);

        if (exists)
        {
            serviceClient.Indexers.Delete(indexer.Name);
        }

        serviceClient.Indexers.Create(indexer);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```
Добавьте следующие строки в Main.

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

Создание индексатора не займет много времени. Несмотря на то что набор данных невелик, аналитические навыки выполняют интенсивные вычисления. Некоторые навыки, такие как анализ изображений, долго выполняются.

> [!TIP]
> Создание индексатора вызывает конвейер. Если есть проблемы с получением данных, сопоставлением входных и выходных данных или порядком операций, они появятся на этом этапе.

### <a name="explore-creating-the-indexer"></a>Подробнее о создании индексатора

В коде для ```"maxFailedItems"``` устанавливается значение -1, которое инструктирует механизм индексирования игнорировать ошибки во время импорта данных. Это полезно, потому что в демонстрационном источнике данных мало документов. Для большего источника данных необходимо установить значение больше 0.

Также обратите внимание, что для ```"dataToExtract"``` задано значение ```"contentAndMetadata"```. Этот оператор указывает индексатору автоматически извлекать содержимое из разных форматов файлов, а также метаданные, относящиеся к каждому файлу.

Когда содержимое будет извлечено, вы можете установить `imageAction` для извлечения текста из изображений, найденных в источнике данных. ```"imageAction"``` с заданной конфигурацией ```"generateNormalizedImages"``` вместе с навыком распознавания текста и навыком объединения текста инструктирует индексатор извлекать текст из изображений (например слово "стоп" из знака остановки движения) и вставлять его как часть поля содержимого. Это относится как к изображениям, встроенным в документы (например, изображение внутри PDF-файлов), так и к изображениям, найденным в источнике данных, например к файлу JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4\. Мониторинг индексирования

После того как индексатор будет определен, он будет выполняться автоматически при отправке запроса. В зависимости от того, какие когнитивные навыки вы определили, индексирование может занять больше времени, чем вы ожидаете. Определить, запущен ли еще индексатор, можно с помощью метода `GetStatus`.

```csharp
private static void CheckIndexerOverallStatus(SearchServiceClient serviceClient, Indexer indexer)
{
    try
    {
        IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", e.Message);
    }
}
```

`IndexerExecutionInfo` представляет текущее состояние и журнал выполнения индексатора.

Предупреждения часто возникают с некоторыми исходными файлами и комбинациями навыков и не всегда указывают на проблему. В этом руководстве предупреждения являются неопасными (например, нет текстовых входных данных из файлов JPEG).

Добавьте следующие строки в Main.

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5\. Поиск

По завершении индексации вы можете выполнить запросы, возвращающие содержимое отдельных полей. По умолчанию служба "Когнитивный поиск Azure" возвращает 50 лучших результатов. Примеры данных малы, поэтому параметр по умолчанию работает нормально. Однако при работе с большими наборами данных вам может потребоваться включить параметры в строку запроса, чтобы вернуть больше результатов. Инструкции см. в статье [How to work with search results in Azure Search](search-pagination-page-layout.md) (Работа с результатами поиска в службе "Поиск Azure").

Для проверки запросите индекс для всех полей.

Добавьте следующие строки в Main.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient` создает экземпляр `SearchIndexClient`, используя значения, которые хранятся в файле конфигурации приложения (appsettings.json). Обратите внимание, что используется ключ API запроса службы поиска, а не ключ администратора.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Добавьте в Main следующий код. Первый командлет try-catch Возвращает определение индекса с именем, типом и атрибутами каждого поля. Второй — параметризованный запрос, где `Select` указывает, какие поля включать в результаты, например `organizations`. Строка поиска `"*"` возвращает все содержимое одного поля.

```csharp
//Verify content is returned after indexing is finished
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
    Console.WriteLine("First query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("First query failed\n Exception message: {0}\n", e.Message);
}

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
    Console.WriteLine("Second query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("Second query failed\n Exception message: {0}\n", e.Message);
}
```

Повторите запрос для дополнительных полей: content, languageCode, keyPhrases и organizations в этом упражнении. Можно вернуть несколько полей с помощью свойства [SELECT](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet) , используя список с разделителями-запятыми.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Сброс и повторный запуск

На ранних экспериментальных этапах разработки самый практичный подход к итерации проектирования — удалить все объекты из службы "Когнитивный поиск Azure" и восстановить их. Имена ресурсов являются уникальными. Удаление объекта позволяет воссоздать его с использованием того же имени.

В этом руководстве объясняется, как проверить наличие индексаторов и индексов, а также как удалить их, чтобы вы могли повторно выполнить код.

Для удаления индексов, индексаторов и наборов навыков вы также можете использовать портал.

## <a name="takeaways"></a>Общие выводы

В этом руководстве описано, как создать расширенный конвейер индексирования путем создания таких компонентов, как источник данных, набор навыков, индекс и индексатор.

Вы получили сведения о [встроенных навыках](cognitive-search-predefined-skills.md), а также об определении набора навыков и механизме построения цепочек навыков путем сопоставления входных и выходных данных. Вы также узнали, что `outputFieldMappings` в определении индексатора требуется для маршрутизации обогащенных значений из конвейера в индекс для поиска в службе "Когнитивный поиск Azure".

Наконец, вы узнали, как тестировать результаты и выполнять сброс системы для дальнейших итераций. Вы узнали, что отправка запросов к индексу возвращает результат, созданный обогащенным конвейером индексирования. Вы также узнали, как проверить состояние индексатора, и какие объекты нужно удалить перед повторным запуском конвейера.

## <a name="clean-up-resources"></a>Очистка ресурсов

Самый быстрый способ очистки после завершения работы с учебником — удалить группу ресурсов, содержащую службу "Когнитивный поиск Azure" и службу BLOB-объектов Azure. Предположим, обе службы находятся в одной группе, в этом случае удалите группу ресурсов, чтобы окончательно удалить все ее содержимое, включая службы и любое хранимое содержимое, созданное для этого руководства. На портале имя группы ресурсов находится на странице "Обзор" каждой службы.

## <a name="next-steps"></a>Следующие шаги

Настройте или расширьте конвейер, добавив пользовательские навыки. Создание пользовательского навыка и добавление его в набор навыков позволяет вам внедрять анализ текста или изображения, который вы пишете сами.

> [!div class="nextstepaction"]
> [Пример. Создание пользовательского навыка для обогащения искусственного интеллекта](cognitive-search-create-custom-skill-example.md)
