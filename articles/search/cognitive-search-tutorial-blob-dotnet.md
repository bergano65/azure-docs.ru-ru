---
title: Руководство по C#. Вызовы API-интерфейсов Cognitive Services в конвейере индексирования — Поиск Azure
description: В этом руководстве представлен пример извлечения данных, обработки естественного языка и изображений с применением искусственного интеллекта в индексировании службы "Поиск Azure" для извлечения и преобразования данных.
manager: eladz
author: MarkHeff
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: maheff
ms.openlocfilehash: 7794cf256ed8063007b4eee7c5c928be85723982
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170200"
---
# <a name="c-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>Руководство по C#. Вызов API-интерфейсов Cognitive Services в конвейере индексирования службы "Поиск Azure"

В этом руководстве вы узнаете о механизме программирования обогащения данных в службе "Поиск Azure" с использованием *когнитивных методик*. Навыки подкрепляются возможностями обработки естественного языка (NLP) и анализа изображений в Cognitive Services. Настроив сочетание и конфигурацию набора навыков, можно извлечь текст и текстовые представления изображения или файл отсканированного документа. Кроме того, вы можете определить язык, сущности, ключевые фразы и многое другое. Конечным результатом является дополнительное содержимое в индексе службы "Поиск Azure", созданное конвейером индексирования с возможностями ИИ.

Из этого руководства вы узнаете, как с помощью пакета SDK для .NET выполнять следующие задачи:

> [!div class="checklist"]
> * Создадите конвейер индексирования, который дополняет пример данных в маршруте к индексу.
> * применение встроенных навыков (оптическое распознавание символов, объединение текста, распознавание языка, разделение текста, распознавание сущностей, извлечение ключевых фраз);
> * Объединять методики, сопоставляя входные данные с результатами в наборе навыков.
> * Выполнить запросы и просмотреть результаты.
> * Сбросить индекс и индексаторы для дальнейшей разработки.

Выходные данные — это полнотекстовый индекс с поддержкой поиска в службе "Поиск Azure". Вы можете улучшить индекс с помощью других стандартных возможностей, таких как [синонимы](search-synonyms.md), [профили оценки](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [анализаторы](search-analyzers.md) и [фильтры](search-filters.md).

В рамках этого учебника используется бесплатная служба, но количество бесплатных транзакций ограничено 20 документами в день. Если вы хотите выполнять описанные здесь операции несколько раз в день, используйте меньший набор файлов.

> [!NOTE]
> По мере расширения области действия путем увеличения частоты обработки, добавления большего количества документов или добавления новых алгоритмов ИИ вам необходимо будет подключить оплачиваемый ресурс Cognitive Services. Плата взимается при вызове API в Cognitive Services и извлечении изображений при открытии документов в службе "Поиск Azure". За извлечение текста из документов плата не взимается.
>
> За операции с применением встроенных навыков взимается [плата по мере использования по тарифам для имеющихся служб Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Плата за извлечение изображений указана на [странице с ценами на Поиск Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

В этом руководстве используются приведенные ниже службы, инструменты и данные. 

+ [Создайте учетную запись хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) для хранения примера данных. Создайте учетную запись хранения в том регионе, где находится служба "Поиск Azure".

+ [Пример данных](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4), который состоит из небольшого набора файлов различных типов. 

+ [Установите Visual Studio](https://visualstudio.microsoft.com/), чтобы использовать решение в качестве интегрированной среды разработки.

+ [Создайте службу "Поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. Вы можете использовать бесплатную службу для выполнения инструкций, описанных в этом учебнике.

## <a name="get-a-key-and-url"></a>Получение ключа и URL-адреса

Для взаимодействия со службой "Поиск Azure" вам потребуется URL-адрес службы и ключ доступа. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **обзора** службы поиска получите URL-адрес. Пример конечной точки может выглядеть так: `https://mydemo.search.windows.net`.

1. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

   ![Получение конечной точки HTTP и ключа доступа](media/search-fiddler/get-url-key.png "Получение конечной точки HTTP и ключа доступа")

Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

## <a name="prepare-sample-data"></a>Подготовка примера данных

Конвейер обогащения извлекает данные из источников данных Azure. Исходные данные должны поступать из поддерживаемого типа источника данных [индексатора службы "Поиск Azure"](search-indexer-overview.md). Хранилище таблиц Azure не поддерживается для когнитивного поиска. В этом упражнении используется хранилище BLOB-объектов, чтобы продемонстрировать несколько типов содержимого.

1. [Войдите на портал Azure](https://portal.azure.com), перейдите к учетной записи хранения Azure, выберите **Большие двоичные объекты**, а затем щелкните **Контейнер**.

1. [Создайте контейнер больших двоичных объектов](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) для хранения примера данных. Можно задать любое из допустимых значений уровня общего доступа. В рамках этого руководства предполагается, что контейнер имеет имя "basic-demo-data-pr".

1. Создав контейнер, откройте его и на панели команд выберите **Отправить**, чтобы отправить [пример данных](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4).

   ![Исходные файлы в хранилище BLOB-объектов Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. После загрузки примеров файлов введите имя контейнера и строку подключения для хранилища BLOB-объектов. Для этого перейдите к учетной записи хранения на портале Azure и выберите **Ключи доступа**, а затем скопируйте значение поля **Строка подключения**.

   Строка подключения должна быть URL-адресом, аналогичным следующему примеру:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Существуют и другие способы указать строку подключения, например предоставить подписанный URL-адрес. Дополнительные сведения об учетных данных источника данных см. в статье [Индексирование документов в хранилище BLOB-объектов Azure с помощью службы поиска Azure](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-your-environment"></a>Настройка среды

Сначала откройте Visual Studio и создайте новый проект консольного приложения, которое будет выполняться на базе .NET Core.

### <a name="install-nuget-packages"></a>Установка пакетов Nuget

[Пакет SDK службы "Поиск Azure" для .NET](https://aka.ms/search-sdk) содержит несколько клиентских библиотек, которые позволяют управлять индексами, источниками данных, индексаторами и наборами навыков, а также отправлять документы, управлять ими и выполнять запросы, не вникая в детали HTTP и JSON. Эти клиентские библиотеки распределяются как пакеты NuGet.

Для этого проекта необходимо установить версию 9 пакета `Microsoft.Azure.Search` NuGet и последнюю версию пакета `Microsoft.Extensions.Configuration.Json` NuGet.

Установите пакет NuGet `Microsoft.Azure.Search` непосредственно из консоли диспетчера пакетов в Visual Studio. Чтобы открыть консоль диспетчера пакетов, последовательно выберите **Инструменты** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**. Чтобы выполнить команду, перейдите к [странице пакета Microsoft.Azure.Search NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search), выберите версию 9 и скопируйте команду диспетчера пакетов. Выполните эту команду консоли диспетчера пакетов.

Чтобы установить пакет NuGet `Microsoft.Extensions.Configuration.Json` в Visual Studio, последовательно выберите **Инструменты** > **Диспетчер пакетов NuGet** > **Управление пакетами NuGet для решения...** . Щелкните "Обзор" и найдите пакет NuGet `Microsoft.Extensions.Configuration.Json`. Выберите его, выберите проект, убедитесь, что используется последняя стабильная версия, и нажмите кнопку "Установить".

## <a name="add-azure-search-service-information"></a>Добавление сведений о службе "Поиск Azure"

Чтобы подключиться к службе "Поиск Azure", вам необходимо добавить в проект сведения о службе поиска. Щелкните правой кнопкой мыши проект в обозревателе решений и последовательно выберите **Добавить** > **Новый элемент...** Присвойте файлу имя `appsettings.json` и нажмите **Добавить**. 

Этот файл необходимо включить в выходной каталог. Для этого щелкните правой кнопкой мыши файл `appsettings.json` и выберите **Свойства**. Измените значение параметра **Копировать в выходной каталог** на **Copy of newer** (Копия более новой версии).

Скопируйте указанный ниже файл JSON в новый файл JSON.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

Укажите службу поиска и добавьте сведения об учетной записи хранения BLOB-объектов.

Сведения о службе поиска содержатся на странице учетной записи поиска на портале Azure. Имя учетной записи будет отображаться на главной странице, а ключи можно найти в разделе **Ключи**.

Чтобы получить строку подключения к большим двоичным объектам, перейдите к учетной записи хранения на портале Azure, выберите **Ключи доступа**, а затем скопируйте значение поля **Строка подключения**.

## <a name="add-namespaces"></a>Добавление пространств имен

В рамках этого руководства используется множество различных типов из разных пространств имен. Чтобы воспользоваться такими типами, добавьте приведенный ниже код в файл `Program.cs`.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>Создание клиента

Создайте экземпляр класса `SearchServiceClient`.

```csharp
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
> 

## <a name="create-a-data-source"></a>Создание источника данных

Вызовите метод `DataSource.AzureBlobStorage`, чтобы создать экземпляр `DataSource`. При использовании метода `DataSource.AzureBlobStorage` нужно указать имя источника данных, строку подключения и имя контейнера больших двоичных объектов.

Политика обратимого удаления не используется в рамках этого руководства, но будет определена в среде. Она применяется для идентификации удаленных больших двоичных объектов на основе значения столбца обратимого удаления. В соответствии с приведенной ниже политикой большой двоичный объект считается удаленным, если у него есть свойство метаданных `IsDeleted` со значением `true`.

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

Теперь объект `DataSource` инициализирован и можно создать источник данных. У класса `SearchServiceClient` есть свойство `DataSources`. Это свойство предоставляет все методы, которые требуются для создания, перечисления, обновления или удаления источников данных Поиска Azure.

При успешном выполнении запроса метод вернет сведения о созданном источнике данных. Если при выполнении запроса возникнет ошибка, например из-за недопустимого параметра, метод вернет исключение.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
```

Так как это ваш первый запрос, на портале Azure убедитесь, что источник данных был создан в службе "Поиск Azure". На странице панели мониторинга службы поиска проверьте наличие нового элемента на плитке "Источники данных". Возможно, вам придется подождать несколько минут, пока обновится страница портала.

  ![Плитка источников данных на портале](./media/cognitive-search-tutorial-blob/data-source-tile.png "Плитка источников данных на портале")

## <a name="create-a-skillset"></a>Создание набора навыков

В рамках этого раздела вы определите набор требуемых шагов обогащения данных. Каждый шаг обогащения называется *навыком*, а набор шагов обогащения — *набором навыков*. В этом руководстве для набора навыков используются [предопределенные когнитивные навыки](cognitive-search-predefined-skills.md):

+ [Оптическое распознавание символов](cognitive-search-skill-ocr.md) для распознавания печатного и рукописного текста в файлах изображений.

+ [Объединение текста](cognitive-search-skill-textmerger.md) для объединения текста из коллекции полей в одном поле.

+ [Распознавание языка](cognitive-search-skill-language-detection.md) для определения языка содержимого.

+ [Разделение текста](cognitive-search-skill-textsplit.md) для разбиения объемного содержимого на более мелкие фрагменты перед вызовом навыка извлечения ключевых фраз и навыка распознавания сущностей. Функции извлечения ключевых фраз и распознавания сущностей принимают входные данные объемом не более 50 000 символов. Некоторые примеры файлов следует разделить, чтобы удовлетворить это ограничение.

+ [Распознавание сущностей](cognitive-search-skill-entity-recognition.md) для извлечения названий организаций из содержимого в контейнере больших двоичных объектов.

+ [Извлечение ключевых фраз](cognitive-search-skill-keyphrases.md) для получения основных ключевых фраз.

В ходе первоначальной обработки Поиск Azure открывает каждый документ для считывания содержимого из разных форматов файлов. Найденный текст в исходном файле помещается в созданное поле ```content```, по одному для каждого документа. Поэтому задайте для входных данных ```"/document/content"```, чтобы использовать этот текст. 

Выходные данные могут быть сопоставлены с индексом, используемым в качестве входных данных для нисходящего навыка, или к обоим, как в случае с кодом языка. В индексе код языка полезен для фильтрации. В качестве входных данных код языка используется навыками анализа текста, чтобы указать лингвистические правила для разбивки слов.

Общие сведения о наборах навыков см. в статье [How to create a skillset in an enrichment pipeline](cognitive-search-defining-skillset.md) (Способ создания набора навыков в конвейере обогащения).

### <a name="ocr-skill"></a>Навык OCR

Навык **оптического распознавания текста** позволяет извлечь текст из изображений. При использовании этого навыка предполагается наличие поля normalized_images. Чтобы создать это поле, далее в руководстве мы зададим для конфигурации ```"imageAction"``` в определении индексатора значение ```"generateNormalizedImages"```.

```csharp
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
```

### <a name="merge-skill"></a>Навык объединения

В рамках этого раздела вы создадите навык **объединения**, который позволяет объединить поле содержимого документа с текстом, полученным с помощью навыка оптического распознавания текста.

```csharp
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
```

### <a name="language-detection-skill"></a>Навык распознавания языка

Навык **распознавания языка** определяет язык введенного текста и сообщает один код языка для каждого документа, отправленного по запросу. Мы будем использовать выходные данные навыка **распознавания языка** в качестве части входных данных для навыка **разделения текста**.

```csharp
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
```

### <a name="text-split-skill"></a>Навык разделения текста

Приведенный ниже код навыка **разделения текста** позволяет разделить текст на страницы и устанавливает для них ограничение в 4000 символов, измеренных методом `String.Length`. С помощью алгоритма текст будет при возможности разделен фрагменты, размер которых не превышает значение `maximumPageLength`. В этом случае будет сделано все возможное, чтобы разделить предложение на его границе. Поэтому размер фрагмента будет немного меньше, чем значение `maximumPageLength`.

```csharp
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
```

### <a name="entity-recognition-skill"></a>Навык распознавания сущностей

Этот экземпляр `EntityRecognitionSkill` настроен для распознавания типа категории `organization`. Навык **распознавания сущностей** также может распознать типы категорий `person` и `location`.

Обратите внимание, что в поле context задается значение ```"/document/pages/*"``` со звездочкой. Это означает, что этап обогащения вызывается для каждой страницы в ```"/document/pages"```.

```csharp
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
```

### <a name="key-phrase-extraction-skill"></a>Навык извлечения ключевых фраз

Как и созданный только что экземпляр `EntityRecognitionSkill`, навык **извлечения ключевых фраз** вызывается для каждой страницы документа.

```csharp
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
```

### <a name="build-and-create-the-skillset"></a>Сборка и создание наборов навыков

Выполните сборку `Skillset` с использованием созданных вами навыков.

```csharp
List<Skill> skills = new List<Skill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

Skillset skillset = new Skillset(
    name: "demoskillset",
    description: "Demo skillset",
    skills: skills);
```

Создайте набор навыков для службы поиска.

```csharp
try
{
    serviceClient.Skillsets.CreateOrUpdate(skillset);
}
catch (Exception e)
{
    // Handle exception
}
```

## <a name="create-an-index"></a>Создание индекса

В этом разделе вы определите схему индекса, указав поля для включения в индекс поиска, а также атрибуты поиска для каждого поля. Поля имеют тип и могут принимать атрибуты, определяющие их использование (поиск, сортировка и т. д.). Имена полей в индексе не обязательно должны точно соответствовать именам полей в источнике. На более позднем этапе вы добавите сопоставления полей в индексаторе для подключения полей "источник — назначение". Для этого шага определите индекс, используя соглашения об именовании полей, относящиеся к вашему поисковому приложению.

В этом упражнении используются следующие поля и типы полей:

| имена полей: | `id`       | Содержимое   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| типы полей: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="create-demoindex-class"></a>Создание класса DemoIndex

Поля для этого индекса определяются с помощью класса модели. Каждое свойство класса модели имеет атрибуты, которые определяют связанные с поиском характеристики соответствующего поля индекса. 

Мы добавим класс модели в новый файл C#. Щелкните проект правой кнопкой мыши и последовательно выберите **Добавить** > **Новый элемент...** . Выберите "Класс", присвойте файлу имя `DemoIndex.cs`, а затем щелкните **Добавить**.

Обязательно укажите, что нужно использовать типы из пространств имен `Microsoft.Azure.Search` и `Microsoft.Azure.Search.Models`.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

Добавьте указанное ниже определение класса модели в файл `DemoIndex.cs` и включите его в то же пространство имен, где будете создавать индекс.

```csharp
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
```

Теперь, когда вы определили класс модели, можно легко создать определение индекса в файле `Program.cs`. Этот индекс будет иметь имя "demoindex".

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

При тестировании вы можете обнаружить, что пытаетесь создать уже существующий индекс. Поэтому предварительно проверяйте, не создан ли он.

```csharp
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

Дополнительные сведения об определении индекса см. в статье [Create Index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Создание индексов (REST API службы поиска Azure)).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Создание индексатора, сопоставление полей и выполнение преобразования

Пока вы создали источник данных, набор навыков и индекс. Эти три компонента становятся частью [индексатора](search-indexer-overview.md), который объединяет каждую часть в единую многофазную операцию. Чтобы связать их вместе в индексаторе, необходимо определить сопоставления полей.

+ Обработка FieldMappings выполняется перед набором навыков. Исходные поля из источника данных сопоставляются с целевыми полями в индексе. Если имена и типы полей одинаковы в исходном и целевом расположениях, сопоставление не требуется.

+ Обработка OutputFieldMappings выполняется после набора навыков. Добавляются ссылки на элементы sourceFieldNames, которые не существуют, пока они не будут созданы при анализе или обогащении документов. TargetFieldName — это поле в индексе.

Помимо привязки входных данных к выходным данным, вы можете использовать сопоставления полей для преобразования структур данных в плоские структуры. Для получения дополнительных сведений см. раздел [Сопоставление обогащенных полей с индексом, поддерживающим поиск](cognitive-search-output-field-mapping.md).

```csharp
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
    // Handle exception
}
```

Создание индексатора не займет много времени. Несмотря на то что набор данных невелик, аналитические навыки выполняют интенсивные вычисления. Некоторые навыки, такие как анализ изображений, долго выполняются.

> [!TIP]
> Создание индексатора вызывает конвейер. Если есть проблемы с получением данных, сопоставлением входных и выходных данных или порядком операций, они появятся на этом этапе.

### <a name="explore-creating-the-indexer"></a>Подробнее о создании индексатора

В коде для ```"maxFailedItems"``` устанавливается значение -1, которое инструктирует механизм индексирования игнорировать ошибки во время импорта данных. Это полезно, потому что в демонстрационном источнике данных мало документов. Для большего источника данных необходимо установить значение больше 0.

Также обратите внимание, что для ```"dataToExtract"``` задано значение ```"contentAndMetadata"```. Этот оператор указывает индексатору автоматически извлекать содержимое из разных форматов файлов, а также метаданные, относящиеся к каждому файлу.

Когда содержимое будет извлечено, вы можете установить `imageAction` для извлечения текста из изображений, найденных в источнике данных. ```"imageAction"``` с заданной конфигурацией ```"generateNormalizedImages"``` вместе с навыком распознавания текста и навыком объединения текста инструктирует индексатор извлекать текст из изображений (например слово "стоп" из знака остановки движения) и вставлять его как часть поля содержимого. Это относится как к изображениям, встроенным в документы (например, изображение внутри PDF-файлов), так и к изображениям, найденным в источнике данных, например к файлу JPG.

## <a name="check-indexer-status"></a>Проверка состояния индексатора

После того как индексатор будет определен, он будет выполняться автоматически при отправке запроса. В зависимости от того, какие когнитивные навыки вы определили, индексирование может занять больше времени, чем вы ожидаете. Определить, запущен ли еще индексатор, можно с помощью метода `GetStatus`.

```csharp
try
{
    IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

    switch (demoIndexerExecutionInfo.Status)
    {
        case IndexerStatus.Error:
            Console.WriteLine("Indexer has error status");
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
    // Handle exception
}
```

`IndexerExecutionInfo` представляет текущее состояние и журнал выполнения индексатора.

Предупреждения часто возникают с некоторыми исходными файлами и комбинациями навыков и не всегда указывают на проблему. В этом руководстве предупреждения являются неопасными (например, нет текстовых входных данных из файлов JPEG).
 
## <a name="verify-content"></a>Проверка содержимого

По завершении индексации вы можете выполнить запросы, возвращающие содержимое отдельных полей. По умолчанию служба "Поиск Azure" возвращает 50 лучших результатов. Примеры данных малы, поэтому параметр по умолчанию работает нормально. Однако при работе с большими наборами данных вам может потребоваться включить параметры в строку запроса, чтобы вернуть больше результатов. Инструкции см. в статье [Разбивка результатов поиска на страницы в службе поиска Azure](search-pagination-page-layout.md).

Для проверки запросите индекс для всех полей.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
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

В результате будет возвращена схема индекса с именем и типом, а также атрибуты каждого поля.

Отправьте второй запрос с `"*"`, чтобы вернуть все содержимое одного поля, например `organizations`.

```csharp
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

Повторите запрос для дополнительных полей: content, languageCode, keyPhrases и organizations в этом упражнении. Вы можете возвратить несколько полей с помощью `$select`, используя разделенный запятыми список.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Сброс и повторный запуск

На ранних экспериментальных этапах разработки самый практичный подход к итерации проектирования — удалить все объекты из службы "Поиск Azure" и восстановить их. Имена ресурсов являются уникальными. Удаление объекта позволяет воссоздать его с использованием того же имени.

В этом руководстве объясняется, как проверить наличие индексаторов и индексов, а также как удалить их, чтобы вы могли повторно выполнить код.

Для удаления индексов, индексаторов и наборов навыков вы также можете использовать портал.

По мере оптимизации кода вам может потребоваться уточнить стратегию восстановления. Дополнительные сведения см. в статье [How to rebuild an Azure Search index](search-howto-reindex.md) (Как восстановить индекс для службы "Поиск Azure").

## <a name="takeaways"></a>Общие выводы

В этом руководстве описано, как создать расширенный конвейер индексирования путем создания таких компонентов, как источник данных, набор навыков, индекс и индексатор.

Вы получили сведения о [предопределенных навыках](cognitive-search-predefined-skills.md), а также определении набора навыков и механизме построения цепочек навыков путем сопоставления входных и выходных данных. Вы также узнали, что `outputFieldMappings` в определении индексатора требуется для маршрутизации обогащенных значений из конвейера в индекс для поиска в службе "Поиск Azure".

Наконец, вы узнали, как тестировать результаты и выполнять сброс системы для дальнейших итераций. Вы узнали, что отправка запросов к индексу возвращает результат, созданный обогащенным конвейером индексирования. Вы также узнали, как проверить состояние индексатора, и какие объекты нужно удалить перед повторным запуском конвейера.

## <a name="clean-up-resources"></a>Очистка ресурсов

Самый быстрый способ очистки после завершения работы с руководством — удалить группу ресурсов, содержащую службу "Поиск Azure" и службу BLOB-объектов Azure. Предположим, обе службы находятся в одной группе, в этом случае удалите группу ресурсов, чтобы окончательно удалить все ее содержимое, включая службы и любое хранимое содержимое, созданное для этого руководства. На портале имя группы ресурсов находится на странице "Обзор" каждой службы.

## <a name="next-steps"></a>Дополнительная информация

Настройте или расширьте конвейер, добавив пользовательские навыки. Создание пользовательского навыка и добавление его в набор навыков позволяет вам внедрять анализ текста или изображения, который вы пишете сами.

> [!div class="nextstepaction"]
> [Пример создания пользовательского навыка с помощью API перевода текста](cognitive-search-create-custom-skill-example.md)
