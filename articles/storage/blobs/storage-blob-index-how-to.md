---
title: Использование тегов индекса больших двоичных объектов для управления и поиска данных в хранилище BLOB-объектов Azure
description: См. примеры использования тегов индекса больших двоичных объектов для категоризации, управления и запроса объектов BLOB.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: hux
ms.custom: devx-track-csharp
ms.openlocfilehash: 41a21545939c5d15c8e2c4034a9648e98aa5a73e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280289"
---
# <a name="utilize-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Использование тегов индекса больших двоичных объектов (Предварительная версия) для управления и поиска данных в хранилище BLOB-объектов Azure

Теги индекса больших двоичных объектов классифицируют данные в учетной записи хранения, используя атрибуты тегов ключ-значение. Эти теги автоматически индексируются и представляются в виде многомерного индекса с поддержкой запросов для упрощения поиска данных. В этой статье показано, как задавать, получать и находить данные с помощью тегов индекса больших двоичных объектов.

Дополнительные сведения о функции индекса больших двоичных объектов см. в статье [Управление и поиск данных в хранилище BLOB-объектов Azure с помощью индекса больших двоичных объектов (Предварительная версия)](storage-manage-find-blobs.md).

> [!NOTE]
> Индекс больших двоичных объектов доступен в общедоступной предварительной версии и доступен в регионах " **Центральная Канада**", " **Восточная Канада**", " **Франция** " и " **Франция** ". Дополнительные сведения об этой функции и известных проблемах и ограничениях см. в статье [Управление и поиск данных в хранилище BLOB-объектов Azure с помощью индекса больших двоичных объектов (Предварительная версия)](storage-manage-find-blobs.md).

## <a name="prerequisites"></a>Предварительные требования
# <a name="portal"></a>[Портал](#tab/azure-portal)
- Подписка зарегистрирована и утверждена для доступа к предварительной версии индекса больших двоичных объектов
- Доступ к [порталу Azure](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)
Так как индекс больших двоичных объектов находится в общедоступной предварительной версии, пакет хранилища .NET выпускается в предварительной версии веб-канала NuGet. Эта библиотека может быть изменена до выхода окончательной версии. 

1. Настройте проект Visual Studio, чтобы начать работу с клиентской библиотекой версии 12 для хранилища BLOB-объектов Azure для .NET. Дополнительные сведения см. в разделе [Краткое руководство по .NET](storage-quickstart-blobs-dotnet.md) .

2. В диспетчере пакетов NuGet найдите пакет **Azure. Storage. BLOB** и установите в проекте версию **12.5.0-Preview. 6** или более новую. Вы также можете выполнить команду ```Install-Package Azure.Storage.Blobs -Version 12.5.0-preview.6```.

   Инструкции см. в разделе [Поиск и установка пакета](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

3. Добавьте в начало файла кода приведенные ниже операторы using.
```csharp
using Azure;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
```
---

## <a name="upload-a-new-blob-with-index-tags"></a>Отправка нового большого двоичного объекта с тегами индекса
# <a name="portal"></a>[Портал](#tab/azure-portal)

1. Выберите свою учетную запись хранения на [портале Azure](https://portal.azure.com/). 

2. Перейдите к параметру **контейнеры** в разделе **Служба BLOB-объектов**, выберите контейнер.

3. Нажмите кнопку **Отправить**, чтобы открыть колонку отправки и в локальной файловой системе найти файл для передачи в виде блочного BLOB-объекта.

4. Разверните раскрывающийся список **Дополнительно** и перейдите к разделу **Теги индекса больших двоичных объектов**.

5. Введите теги индекса больших двоичных объектов, которые необходимо применить к данным, в виде пар "ключ-значение".

6. Нажмите кнопку **Отправить**, чтобы отправить большой двоичный объект.

![Отправка данных с тегами индекса больших двоичных объектов](media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png)

# <a name="net"></a>[.NET](#tab/net)

В приведенном ниже примере показано, как создать добавочный большой двоичный объект с тегами, заданными во время создания.

```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");

          // Blob index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>Получение, задание и обновление тегов индекса больших двоичных объектов
# <a name="portal"></a>[Портал](#tab/azure-portal)

1. Выберите свою учетную запись хранения на [портале Azure](https://portal.azure.com/). 

2. Перейдите к параметру **Контейнеры** в разделе **Служба BLOB-объектов** и выберите контейнер.

3. Выберите нужный большой двоичный объект из списка больших двоичных объектов в выбранном контейнере.

4. На вкладке общих сведений о большом двоичном объекте отображаются его свойства, включая все **теги индекса больших двоичных объектов**.

5. Вы можете получить, задать, изменить или удалить любые теги индекса в виде пар "ключ-значение" для большого двоичного объекта.

6. Нажмите кнопку **Сохранить**, чтобы подтвердить изменения большого двоичного объекта.

![Получение, задание, изменение и удаление тегов индекса больших двоичных объектов](media/storage-blob-index-concepts/blob-index-get-set-tags.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or update blob index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get blob index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List blobs with all options returned including blob index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing blob index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>Фильтрация и поиск данных с помощью тегов индекса больших двоичных объектов

# <a name="portal"></a>[Портал](#tab/azure-portal)

В портал Azure фильтр тегов индекса больших двоичных объектов автоматически применяет `@container` параметр к области действия выбранного контейнера. Если необходимо отфильтровать и найти данные с тегами во всей учетной записи хранения, используйте REST API, пакеты SDK или другие средства.

1. Выберите свою учетную запись хранения на [портале Azure](https://portal.azure.com/). 

2. Перейдите к параметру **контейнеры** в разделе **Служба BLOB-объектов**, выберите контейнер.

3. Нажмите кнопку **Фильтр тегов индекса больших двоичных объектов** для фильтрации в выбранном контейнере.

4. Введите ключ тега индекса BLOB-объекта и значение тега

5. Нажмите кнопку **Фильтр тегов индекса больших двоичных объектов**, чтобы добавить дополнительные фильтры тегов (до 10).

![Фильтрация и поиск объектов с помощью тегов индекса больших двоичных объектов](media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<FilterBlobItem> blobs = new List<FilterBlobItem>();
          await foreach (FilterBlobItem filterBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(filterBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Управление жизненным циклом с помощью фильтров тегов индекса больших двоичных объектов

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. Выберите свою учетную запись хранения на [портале Azure](https://portal.azure.com/). 

2. Перейдите к параметру **Управление жизненным циклом** в разделе **Служба BLOB-объектов**.

3. Выберите *Добавить правило*, а затем заполните поля формы набора действий.

4. Выберите **Фильтр** установить, чтобы добавить необязательный фильтр для сопоставления префиксов и индексов больших двоичных объектов ![ Добавление фильтров тегов индекса BLOB-объектов для управления жизненным циклом](media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png)

5. Выберите команду **Просмотреть и добавить**, чтобы проверить параметры правила ![Пример правила управления жизненным циклом с фильтром тегов индекса больших двоичных объектов](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png).

6. Выберите **Добавить**, чтобы применить новое правило к политике управления жизненным циклом.

# <a name="net"></a>[.NET](#tab/net)
Политики [управления жизненным циклом](storage-lifecycle-management-concepts.md) применяются к каждой учетной записи хранения на уровне управления. Для .NET установите [библиотеку хранилища Microsoft Azure Management 16.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) или более поздней версии, чтобы воспользоваться фильтром совпадения больших двоичных объектов в правиле управления жизненным циклом.

---

## <a name="next-steps"></a>Дальнейшие действия

 - Дополнительные сведения о индексе больших двоичных объектов см. [в статье управление и поиск данных в хранилище BLOB-объектов Azure с помощью индекса BLOB (Предварительная версия)](storage-manage-find-blobs.md )
 - Узнайте больше об управлении жизненным циклом. См. статью [Управление жизненным циклом хранилища BLOB-объектов Azure](storage-lifecycle-management-concepts.md).
