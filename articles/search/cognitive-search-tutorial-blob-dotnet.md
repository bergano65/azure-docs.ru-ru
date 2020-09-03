---
title: Руководство по C#. Использование ИИ с большими двоичными объектами Azure
titleSuffix: Azure Cognitive Search
description: Узнайте, как выполнять извлечение текста и обработку естественного языка по содержимому хранилища BLOB-объектов, используя C# и пакет SDK .NET для Когнитивного поиска Azure.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 08/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 09273cf901830e850acca05c57c0b110ffd7e28a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89002868"
---
# <a name="tutorial-ai-generated-searchable-content-from-azure-blobs-using-the-net-sdk"></a>Руководство по Работа с созданным ИИ содержимым с возможностью поиска из больших двоичных объектов Azure с использованием пакета SDK для .NET

Если у вас есть неструктурированный текст или изображения в хранилище BLOB-объектов Azure, [конвейер обогащения с помощью искуственного интелекта](cognitive-search-concept-intro.md) поможет извлекать информацию и создавать содержимое, применимое для сценариев полнотекстового поиска и интеллектуального анализа. В этом учебнике для C# внимание уделяется созданию полей на основе распознавания текста (OCR) изображений и обработки естественного языка. Все это позволит вам использовать информацию в запросах, аспектах и фильтрах.

В этом учебнике используется C# и пакет [SDK для .NET](/dotnet/api/overview/azure/search) для выполнения следующих задач:

> [!div class="checklist"]
> * Начните с файлов приложений и изображений в хранилище BLOB-объектов Azure.
> * Определите конвейер для добавления возможностей OCR, извлечения текста, определения языка, распознавания сущностей и ключевых фраз.
> * Определите индекс для хранения выходных данных (необработанное содержимое и созданные конвейером пары "имя — значение").
> * Выполните конвейер, чтобы начать преобразования и анализ, а также создать и загрузить индекс.
> * Изучите результаты с помощью полнотекстового поиска и расширенного синтаксиса запросов.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

+ [Хранилище Azure](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Создайте службу поиска](search-create-service-portal.md) или [найдите существующую службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Для выполнения инструкций из этого руководства вы можете использовать бесплатную версию службы. В бесплатной версии вы можете использовать не более трех индексов, трех индексаторов и трех источников данных. В этом руководстве создается по одному объекту из каждой категории. Перед началом работы убедитесь, что у службы есть достаточно места, чтобы принять новые ресурсы.

## <a name="download-files"></a>Загрузка файлов

1. Откройте эту [папку OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) и щелкните **Скачать** вверху слева, чтобы скопировать файлы на локальный компьютер. 

1. Щелкните ZIP-файл правой кнопкой мыши и выберите **Извлечь все**. Доступно 14 файлов разных типов. В этом примере вам потребуются семь из них.

Вы также можете скачать исходный код для этого руководства. Исходный код находится в папке tutorial-ai-enrichment в репозитории [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="1---create-services"></a>1\. Создание служб

В этом учебнике используются Когнитивный поиск Azure для индексирования и создания запросов, сервер Cognitive Services — для обогащения с помощью искусственного интеллекта и хранилище BLOB-объектов Azure — для предоставления данных. В этом учебнике остается свободное выделение 20 транзакций на индексатор в день в Cognitive Services, поэтому необходимо создать только службы поиска и хранения.

Желательно создать все службы в одном регионе и одной группе ресурсов, чтобы упростить взаимодействие и управление. В реальной системе учетная запись хранения Azure может находиться в любом регионе.

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

1. Щелкните **+ Контейнер**, чтобы создать контейнер, и присвойте ему имя *cog-search-demo*.

1. Выберите *cog-search-demo* и щелкните **Отправить**, чтобы открыть папку с сохраненными файлами для скачивания. Выберите все четырнадцать файлов и нажмите кнопку **ОК** для отправки.

   ![Отправка образцов файлов](media/cognitive-search-quickstart-blob/sample-data.png "Отправка образцов файлов")

1. Прежде чем выйти из службы хранилища Azure, получите строку подключения для создания подключения в Когнитивном поиске Azure. 

   1. Вернитесь на страницу обзора учетной записи хранения (в качестве примера мы использовали *blobstoragewestus*). 
   
   1. В области навигации слева выберите **Ключи доступа** и скопируйте одну из строк подключения. 

   Строка подключения — это URL-адрес в следующем формате:

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Сохраните эту строку подключения в Блокноте. Она понадобится позже при настройке подключения к источнику данных.

### <a name="cognitive-services"></a>Cognitive Services

Обогащение ИИ основано на платформе Cognitive Services, которая включает службы "Анализ текста" для обработки естественного языка и "Компьютерное зрение" для обработки изображений. Если бы вы создавали реальный прототип или проект, на этом этапе нужно было бы создать Cognitive Services (в том же регионе, что и Когнитивный поиск Azure) для связывания с операциями индексирования.

Но для нашего примера подготовку ресурсов можно пропустить, так как Когнитивный поиск Azure может подключаться к Cognitive Services в фоновом режиме и предоставляет 20 бесплатных транзакций для каждого выполнения индексатора. Так как в этом руководстве используется 14 транзакций, будет достаточно выделения на уровне "Бесплатный". Для крупных проектов вам, скорее всего, нужно будет подготовить Cognitive Services на уровне S0 с оплатой по мере использования. См. сведения о [подключении Cognitive Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Когнитивный поиск Azure

Третий компонент — Когнитивный поиск Azure, который вы можете [создать на портале](search-create-service-portal.md). Для выполнения действий в этом пошаговом руководстве можно использовать уровень "Бесплатный". 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Получение ключа API и URL-адреса конечной точки для администрирования Когнитивного поиска Azure

Для взаимодействия со службой "Когнитивный поиск Azure" вам нужны URL-адрес службы и ключ доступа. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Когнитивный поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **обзора** службы поиска получите URL-адрес. Пример конечной точки может выглядеть так: `https://mydemo.search.windows.net`.

1. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

   Получите также ключ запроса. Мы рекомендуем создавать запросы с доступом только для чтения.

   ![Получение имени службы, ключей запросов и администратора](media/search-get-started-nodejs/service-name-and-keys.png)

Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

## <a name="2---set-up-your-environment"></a>2\. Настройка среды

Сначала откройте Visual Studio и создайте новый проект консольного приложения, которое будет выполняться на базе .NET Core.

### <a name="install-nuget-packages"></a>Установка пакетов Nuget

[Пакет SDK службы "Когнитивный поиск Azure" для .NET](/dotnet/api/overview/azure/search) содержит несколько клиентских библиотек, которые позволяют управлять индексами, источниками данных, индексаторами и наборами навыков, а также отправлять документы, управлять ими и выполнять запросы, не вникая в детали HTTP и JSON. Эти клиентские библиотеки распределяются как пакеты NuGet.

Для этого проекта установите пакет NuGet `Microsoft.Azure.Search` версии 9 или более поздней.

1. В браузере перейдите на [страницу пакета NuGet Microsoft.Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search).

1. Выберите последнюю версию (9 или более позднюю).

1. Скопируйте команду диспетчера пакетов.

1. Откройте консоль диспетчера пакетов. Выберите **Инструменты** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**. 

1. Вставьте и выполните команду, скопированную на предыдущем шаге.

Затем установите последнюю версию пакета NuGet `Microsoft.Extensions.Configuration.Json`.

1. Выберите **Инструменты** > **Диспетчер пакетов NuGet** > **Управление пакетами NuGet для решения...** . 

1. Щелкните **Обзор** и найдите пакет NuGet `Microsoft.Extensions.Configuration.Json`. 

1. Выберите пакет, выберите проект, убедитесь, что используется последняя стабильная версия, и нажмите кнопку **Установить**.

### <a name="add-service-connection-information"></a>Добавление сведений о подключении службы

1. Щелкните правой кнопкой мыши проект в обозревателе решений и последовательно выберите **Добавить** > **Новый элемент...** . 

1. Присвойте файлу имя `appsettings.json` и нажмите **Добавить**. 

1. Добавьте файл в каталог выходных данных.
    1. Щелкните правой кнопкой мыши `appsettings.json` и выберите пункт **Свойства**. 
    1. Измените значение параметра **Копировать в выходной каталог** на **Копировать более новые**.

1. Скопируйте указанный ниже файл JSON в новый файл JSON.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```
    
Укажите службу поиска и добавьте сведения об учетной записи хранения BLOB-объектов. Помните, что эти сведения можно получить во время выполнения действий по подготовке службы из предыдущего раздела.

Для **SearchServiceName** введите краткое имя службы, а не полный URL-адрес.

### <a name="add-namespaces"></a>Добавление пространств имен

В файле `Program.cs` добавьте следующие пространства имен.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Создание клиента

Создайте экземпляр класса `SearchServiceClient` в разделе `Main`.

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

### <a name="add-function-to-exit-the-program-during-failure"></a>Добавление функции для выхода из программы во время сбоя

Этот учебник поможет вам разобраться в каждом этапе работы конвейера индексирования. Если произошла критическая ошибка, которая не позволяет программе создать источник данных, набор навыков, индекс или индексатор, программа выдаст сообщение об ошибке и выполнит выход, чтобы можно было разобраться в ее причине и устранить ее.

Добавьте `ExitProgram` в `Main`, чтобы обработать сценарии, требующие завершения работы программы.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3\. Создание конвейера

В Когнитивном поиске Azure обработка ИИ выполняется во время индексирования (или приема данных). В этой части пошагового руководства описано, как создать четыре объекта: источник данных, определение индекса, набор навыков и индексатор. 

### <a name="step-1-create-a-data-source"></a>Шаг 1. Создание источника данных

У класса `SearchServiceClient` есть свойство `DataSources`. Это свойство предоставляет все методы, которые требуются для создания, перечисления, обновления или удаления источников данных Когнитивного поиска Azure.

Вызовите метод `serviceClient.DataSources.CreateOrUpdate(dataSource)`, чтобы создать экземпляр `DataSource`. При использовании метода `DataSource.AzureBlobStorage` нужно указать имя источника данных, строку подключения и имя контейнера больших двоичных объектов.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "cog-search-demo",
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

Теперь добавьте строку в раздел `Main`, чтобы вызвать только что добавленную функцию `CreateOrUpdateDataSource`.

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

Выполните сборку и запуск решения. Так как это ваш первый запрос, на портале Azure убедитесь, что источник данных был создан в службе "Когнитивный поиск Azure". На странице панели мониторинга службы поиска проверьте наличие нового элемента на плитке "Источники данных". Возможно, вам придется подождать несколько минут, пока обновится страница портала.

  ![Плитка источников данных на портале](./media/cognitive-search-tutorial-blob/data-source-tile.png "Плитка источников данных на портале")

### <a name="step-2-create-a-skillset"></a>Шаг 2. Создание набора навыков

В рамках этого раздела вы определите набор требуемых шагов обогащения данных. Каждый шаг обогащения называется *навыком*, а набор шагов обогащения — *набором навыков*. В этом руководстве для набора навыков используются [встроенные когнитивные навыки](cognitive-search-predefined-skills.md):

+ [Оптическое распознавание символов](cognitive-search-skill-ocr.md) для распознавания печатного и рукописного текста в файлах изображений.

+ [Объединение текста](cognitive-search-skill-textmerger.md) для объединения текста из коллекции полей в одном поле.

+ [Распознавание языка](cognitive-search-skill-language-detection.md) для определения языка содержимого.

+ [Разделение текста](cognitive-search-skill-textsplit.md) для разбиения объемного содержимого на более мелкие фрагменты перед вызовом навыка извлечения ключевых фраз и навыка распознавания сущностей. Функции извлечения ключевых фраз и распознавания сущностей принимают входные данные объемом не более 50 000 символов. Некоторые примеры файлов следует разделить, чтобы удовлетворить это ограничение.

+ [Распознавание сущностей](cognitive-search-skill-entity-recognition.md) для извлечения названий организаций из содержимого в контейнере больших двоичных объектов.

+ [Извлечение ключевых фраз](cognitive-search-skill-keyphrases.md) для получения основных ключевых фраз.

В ходе первоначальной обработки Когнитивный поиск Azure открывает каждый документ для считывания содержимого из разных форматов файлов. Найденный текст в исходном файле помещается в созданное поле ```content```, по одному для каждого документа. Поэтому задайте для входных данных ```"/document/content"```, чтобы использовать этот текст. 

Выходные данные могут быть сопоставлены с индексом, используемым в качестве входных данных для нисходящего навыка, или к обоим, как в случае с кодом языка. В индексе код языка полезен для фильтрации. В качестве входных данных код языка используется навыками анализа текста, чтобы указать лингвистические правила для разбивки слов.

Общие сведения о наборах навыков см. в статье [How to create a skillset in an enrichment pipeline](cognitive-search-defining-skillset.md) (Способ создания набора навыков в конвейере обогащения).

### <a name="ocr-skill"></a>Навык OCR

Навык **оптического распознавания текста** позволяет извлечь текст из изображений. При использовании этого навыка предполагается наличие поля normalized_images. Чтобы создать это поле, далее в руководстве мы зададим для конфигурации ```"imageAction"``` в определении индексатора значение ```"generateNormalizedImages"```.

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "image",
        source: "/document/normalized_images/*")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "text",
        targetName: "text")
    };

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
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/content"),
        new InputFieldMappingEntry(
        name: "itemsToInsert",
        source: "/document/normalized_images/*/text"),
        new InputFieldMappingEntry(
        name: "offsets",
        source: "/document/normalized_images/*/contentOffset")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "mergedText",
        targetName: "merged_text")
    };

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
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "languageCode",
        targetName: "languageCode")
    };

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
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"),
        new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "textItems",
        targetName: "pages")
    };

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
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "organizations",
        targetName: "organizations")
    };

    List<EntityCategory> entityCategory = new List<EntityCategory>
    {
        EntityCategory.Organization
    };

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

Добавьте в раздел `Main` следующие строки.

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
    List<Skill> skills = new List<Skill>
    {
        ocrSkill,
        mergeSkill,
        languageDetectionSkill,
        splitSkill,
        entityRecognitionSkill,
        keyPhraseExtractionSkill
    };

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>Шаг 3. Создание индекса

В этом разделе вы определите схему индекса, указав поля для включения в индекс поиска, а также атрибуты поиска для каждого поля. Поля имеют тип и могут принимать атрибуты, определяющие их использование (поиск, сортировка и т. д.). Имена полей в индексе не обязательно должны точно соответствовать именам полей в источнике. На более позднем этапе вы добавите сопоставления полей в индексаторе для подключения полей "источник — назначение". Для этого шага определите индекс, используя соглашения об именовании полей, относящиеся к вашему поисковому приложению.

В этом упражнении используются следующие поля и типы полей:

| Имена полей | Типы полей |
| --- | --- |
| идентификатор | Edm.String |
| content | Edm.String |
| languageCode | Edm.String |
| keyPhrases | List<Edm.String> |
| organizations | List<Edm.String> |

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

Теперь, когда вы определили класс модели, можно легко создать определение индекса в файле `Program.cs`. У этого индекса будет имя `demoindex`. Если индекс с таким именем уже существует, он будет удален.

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

Добавьте в раздел `Main` следующие строки.

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Microsoft.Azure.Search.Models.Index demoIndex = CreateDemoIndex(serviceClient);
```

Добавьте следующую инструкцию using, чтобы устранить неоднозначность ссылки.

```csharp
using Index = Microsoft.Azure.Search.Models.Index;
```

Дополнительные сведения об определении индекса см. в статье [Create Index (Azure Search Service REST API)](/rest/api/searchservice/create-index) (Создание индексов (REST API службы "Поиск Azure")).

### <a name="step-4-create-and-run-an-indexer"></a>Шаг 4. Создание и запуск индексатора

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

    List<FieldMapping> fieldMappings = new List<FieldMapping>
    {
        new FieldMapping(
        sourceFieldName: "metadata_storage_path",
        targetFieldName: "id",
        mappingFunction: new FieldMappingFunction(
            name: "base64Encode")),
        new FieldMapping(
        sourceFieldName: "content",
        targetFieldName: "content")
    };

    List<FieldMapping> outputMappings = new List<FieldMapping>
    {
        new FieldMapping(
        sourceFieldName: "/document/pages/*/organizations/*",
        targetFieldName: "organizations"),
        new FieldMapping(
        sourceFieldName: "/document/pages/*/keyPhrases/*",
        targetFieldName: "keyPhrases"),
        new FieldMapping(
        sourceFieldName: "/document/languageCode",
        targetFieldName: "languageCode")
    };

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
Добавьте в раздел `Main` следующие строки.

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer and executing the pipeline...");
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

Добавьте в раздел `Main` следующие строки.

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5\. Поиск

По завершении индексации вы можете выполнить запросы, возвращающие содержимое отдельных полей. По умолчанию служба "Когнитивный поиск Azure" возвращает 50 лучших результатов. Примеры данных малы, поэтому параметр по умолчанию работает нормально. Однако при работе с большими наборами данных вам может потребоваться включить параметры в строку запроса, чтобы вернуть больше результатов. Инструкции см. в статье [How to work with search results in Azure Search](search-pagination-page-layout.md) (Работа с результатами поиска в службе "Поиск Azure").

Для проверки запросите индекс для всех полей.

Добавьте в раздел `Main` следующие строки.

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

`CreateSearchIndexClient` создает экземпляр `SearchIndexClient`, используя значения, которые хранятся в файле конфигурации приложения (appsettings.json). Обратите внимание, что используется ключ API запросов службы поиска, а не ключ администратора.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Добавьте в раздел `Main` следующий код. Первый запрос try-catch возвращает определение индекса с именем и типом, а также атрибуты каждого поля. Второй — параметризованный запрос, где `Select` указывает, какие поля включать в результаты, например `organizations`. Строка поиска `"*"` возвращает все содержимое одного поля.

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

Повторите запрос для дополнительных полей: content, languageCode, keyPhrases и organizations в этом упражнении. Вы можете возвратить несколько полей с помощью свойства [Select](/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet), используя разделенный запятыми список.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Сброс и повторный запуск

На ранних экспериментальных этапах разработки самый практичный подход к итерации схемы — удалить все объекты из службы "Когнитивный поиск Azure" и восстановить их с помощью кода. Имена ресурсов являются уникальными. Удаление объекта позволяет воссоздать его с использованием того же имени.

Пример кода для этого учебника проверяет имеющиеся объекты и удаляет их, чтобы вы могли повторно выполнить код.

Для удаления индексов, индексаторов, источников данных и наборов навыков также можно использовать портал.

## <a name="takeaways"></a>Общие выводы

В этом руководстве описано, как создать расширенный конвейер индексирования путем создания таких компонентов, как источник данных, набор навыков, индекс и индексатор.

Вы получили сведения о [встроенных навыках](cognitive-search-predefined-skills.md), а также об определении набора навыков и механизме построения цепочек навыков путем сопоставления входных и выходных данных. Вы также узнали, что `outputFieldMappings` в определении индексатора требуется для маршрутизации обогащенных значений из конвейера в индекс для поиска в службе "Когнитивный поиск Azure".

Наконец, вы узнали, как тестировать результаты и выполнять сброс системы для дальнейших итераций. Вы узнали, что отправка запросов к индексу возвращает результат, созданный обогащенным конвейером индексирования. Вы также узнали, как проверить состояние индексатора, и какие объекты нужно удалить перед повторным запуском конвейера.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы работаете в своей подписке, после завершения проекта целесообразно удалить созданные ресурсы, которые вам больше не потребуются. Работающие ресурсы могут означать лишние затраты. Можно удалить отдельные ресурсы или удалить группу ресурсов, что позволит удалить весь набор ресурсов.

Просматривать ресурсы и управлять ими можно на портале с помощью ссылок "Все ресурсы" или "Группы ресурсов" в области навигации слева.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы знакомы со всеми объектами в конвейере обогащения с помощью ИИ, давайте более подробно рассмотрим определения набора навыков и отдельные навыки.

> [!div class="nextstepaction"]
> [Создание набора навыков](cognitive-search-defining-skillset.md)