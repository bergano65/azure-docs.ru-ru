---
title: Фильтруя данные с помощью ускорения запроса на хранение данных Azure Data Lake (предварительный просмотр) Документы Майкрософт
description: Используйте ускорение запроса (предварительный просмотр) для получения подмножества данных из учетной записи хранилища.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: ae3dfc7681ef0d8ce3fcf679bddbd0ff195f4e3b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771850"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Фильтруя данные с помощью ускорения запроса на хранение данных Azure Data Lake (предварительный просмотр)

В этой статье показано, как использовать ускорение запроса (предварительный просмотр) для получения подмноза данных из учетной записи хранилища. 

Ускорение запросов (предварительный просмотр) — это новая возможность для хранения озер данных Azure Data, которая позволяет приложениям и системам аналитики резко оптимизировать обработку данных, извлекая только те данные, которые им необходимы для выполнения данной операции. Чтобы узнать больше, смотрите [ускорение ускорения хранения данных Azure Data Lake (предварительный просмотр)](data-lake-storage-query-acceleration.md).

> [!NOTE]
> Функция ускорения запроса находится в открытом доступе и доступна в центральных регионах Канады и Франции. Для проверки ограничений смотрите статью [«Известные проблемы».](data-lake-storage-known-issues.md) Чтобы зарегистрироваться в предварительном просмотре, [см.](https://aka.ms/adls/qa-preview-signup)  

## <a name="prerequisites"></a>Предварительные требования

### <a name="net"></a>[.NET](#tab/dotnet)

- Для доступа к службе хранилища Azure требуется подписка Azure. Если у вас еще нет подписки, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

- Учетная запись **v2 общего назначения.** увидеть [Создать учетную запись хранения](../common/storage-quickstart-create-account.md).

- [.NET SDK](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- Для доступа к службе хранилища Azure требуется подписка Azure. Если у вас еще нет подписки, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

- Учетная запись **v2 общего назначения.** увидеть [Создать учетную запись хранения](../common/storage-quickstart-create-account.md).

- [комплект SDK для Java (JDK)](/java/azure/jdk/?view=azure-java-stable) версии 8 или более поздней версии.

- [Апач Мавен](https://maven.apache.org/download.cgi). 

  > [!NOTE] 
  > В этой статье предполагается, что вы создали проект Java с помощью Apache Maven. Например, как создать проект с помощью Apache Maven, [см.](storage-quickstart-blobs-java.md#setting-up)
  
---

## <a name="install-packages"></a>Установка пакетов 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Скачать пакеты ускорения запроса. Вы можете получить сжатый файл .zip, который содержит [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net)эти пакеты, используя эту ссылку: . 

2. Извлеките содержимое этого файла в каталог проекта.

3. Откройте файл проекта *(.csproj)* в текстовом редакторе и \<\> добавьте эти ссылки на пакет внутри элемента Project.

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Восстановить пакеты Предварительного просмотра SDK. Этот пример команды восстанавливает пакеты SDK `dotnet restore` предварительного просмотра с помощью команды. 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Восстановить все другие зависимости из общего репозитория NuGet.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Создайте каталог в корне проекта. Корневой каталог — это каталог, содержащий файл **pom.xml.**

   > [!NOTE]
   > Примеры в этой статье предполагают, что название каталога **lib**.

2. Скачать пакеты ускорения запроса. Вы можете получить сжатый файл .zip, который содержит [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java)эти пакеты, используя эту ссылку: . 

3. Извлеките файлы в этом файле .zip в созданный каталог. В нашем примере этот каталог называется **lib**. 

4. Откройте файл *pom.xml* в текстовом редакторе. Добавьте приведенные ниже элементы зависимости в группу зависимостей. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-core-http-netty</artifactId>
      <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>org.apache.avro</groupId>
      <artifactId>avro</artifactId>
      <version>1.9.2</version>
   </dependency>
   <dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.8</version>
   </dependency>
   <!-- Local dependencies -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-blob</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-blob-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-common</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-common-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-quickquery</artifactId>
       <version>12.0.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-quickquery-12.0.0-beta.1.jar</systemPath>
   </dependency>
   ```

---

## <a name="add-statements"></a>Добавление инструкций


### <a name="net"></a>[.NET](#tab/dotnet)

Добавьте `using` эти инструкции в верхнюю часть файла кода.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

Ускорение запроса получает отформатированные данные CSV и Json. Поэтому не забудьте добавить с помощью инструкций для любых библиотек CSV или Json, которые вы хотите использовать. Примеры, которые появляются в этой статье, разбирают файл CSV с помощью библиотеки [CsvHelper,](https://www.nuget.org/packages/CsvHelper/) которая доступна на NuGet. Поэтому мы добавим `using` эти инструкции в верхнюю часть файла кода.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Для составления примеров, приведенных в этой `using` статье, необходимо также добавить эти утверждения.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Добавьте `import` эти инструкции в верхнюю часть файла кода.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import com.azure.storage.quickquery.*;
import com.azure.storage.quickquery.models.*;
import java.io.*;
import org.apache.commons.csv.*;
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Извлечение данных с помощью фильтра

Можно использовать S'L для указания предикатов фильтра строки и проекций столбцов в запросе ускорения запроса. Следующий код запрашивает файл CSV в хранилище и возвращает все строки `Hemingway, Ernest`данных, где третья колонка совпадает с значением. 

- В запрашиваемом файле для `BlobStorage` s'L ключевое слово используется для обозначения запрашиваемого файла.

- Ссылки столбцов `_N` указаны как `_1`место, где находится первый столбец. Если исходный файл содержит строку заголовка, то можно обратиться к столбику по имени, указанному в строке заголовка. 

### <a name="net"></a>[.NET](#tab/dotnet)

Метод `BlobQuickQueryClient.QueryAsync` async отправляет запрос на API ускорения запроса, а затем передает результаты обратно в приложение в качестве объекта [stream.](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8)

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        using (var reader = new StreamReader((await blob.GetQuickQueryClient().QueryAsync(query,
                new CsvTextConfiguration() { HasHeaders = headers }, 
                new CsvTextConfiguration() { HasHeaders = false }, 
                new ErrorHandler(),
                new BlobRequestConditions(), 
                new ProgressHandler(),
                CancellationToken.None)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) 
            { HasHeaderRecord = false }))
            {
                while (await parser.ReadAsync())
                {
                    parser.Context.Record.All(cell =>
                    {
                        Console.Out.Write(cell + "  ");
                        return true;
                    });
                    Console.Out.WriteLine();
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

class ErrorHandler : IBlobQueryErrorReceiver
{
    public void ReportError(BlobQueryError err)
    {
        Console.Error.WriteLine(String.Format("Error: {1}:{2}", err.Name, err.Description));
    }
}

class ProgressHandler : IProgress<long>
{
    public void Report(long value)
    {
        Console.Error.WriteLine("Bytes scanned: " + value.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

Метод `BlobQuickQueryClient.openInputStream()` отправляет запрос на API ускорения запроса, а затем передает результаты обратно `InputStream` в приложение в качестве объекта, который может быть прочитан, как и любой другой объект InputStream.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    DumpQueryCsv(blobClient, expression, false);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
    
        BlobQuickQueryDelimitedSerialization input = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');

        BlobQuickQueryDelimitedSerialization output = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(false)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
                
        BlobRequestConditions requestConditions = null;
        /* ErrorReceiver determines what to do on errors. */
        ErrorReceiver<BlobQuickQueryError> errorReceiver = System.out::println;

        /* ProgressReceiver details how to log progress*/
        com.azure.storage.common.ProgressReceiver progressReceiver = System.out::println;
    
        /* Create a query acceleration client to the blob. */
        BlobQuickQueryClient qqClient = new BlobQuickQueryClientBuilder(blobClient)
            .buildClient();
        /* Open the query input stream. */
        InputStream stream = qqClient.openInputStream(query, input, output, requestConditions, errorReceiver, progressReceiver);
            
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL)) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
    }
}
```

---

## <a name="retrieve-specific-columns"></a>Извлечение определенных столбцов

Вы можете присвоить результаты подмножестве столбцов. Таким образом, вы получаете только столбцы, необходимые для выполнения данного вычисления. Это повышает производительность приложения и снижает затраты, поскольку по сети передается меньше данных. 

Этот код получает `PublicationYear` только столбец для всех книг в наборе данных. Он также использует информацию из строки заголовка в исходном файле для эталонных столбцов в запросе.


### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryPublishDates(BlockBlobClient blob)
{
    string query = @"SELECT PublicationYear FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryPublishDates(BlobClient blobClient)
{
    String expression = "SELECT PublicationYear FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

Следующий код сочетает в себе строковую фильтрацию и проекции столбцов в один и тот же запрос. 

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryMysteryBooks(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryMysteryBooks(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

## <a name="next-steps"></a>Следующие шаги

- [Форма ускорения запроса](https://aka.ms/adls/queryaccelerationpreview)    
- [Ускорение запроса на хранение данных Azure Data Lake (предварительный просмотр)](data-lake-storage-query-acceleration.md)
- [Ссылка на ускорение запросов s'L (предварительный просмотр)](query-acceleration-sql-reference.md)
- Ссылка на ускорение запроса REST API
