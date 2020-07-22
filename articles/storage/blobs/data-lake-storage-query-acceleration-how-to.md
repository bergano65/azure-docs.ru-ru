---
title: Фильтрация данных с помощью ускорения запросов Azure Data Lake Storage (Предварительная версия) | Документация Майкрософт
description: Используйте ускорение запросов (Предварительная версия) для получения подмножества данных из вашей учетной записи хранения.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: cc9235f07c0829abfb8be42e83d05d8428bc1806
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84465870"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Фильтрация данных с помощью ускорения запросов Azure Data Lake Storage (Предварительная версия)

В этой статье показано, как использовать ускорение запросов (Предварительная версия) для получения подмножества данных из учетной записи хранения. 

Ускорение запросов (Предварительная версия) — это новая возможность для Azure Data Lake Storage, которая позволяет приложениям и платформам аналитики значительно оптимизировать обработку данных, получая только те данные, которые необходимы для выполнения определенной операции. Дополнительные сведения см. в разделе [ускорение запросов Azure Data Lake Storage (Предварительная версия)](data-lake-storage-query-acceleration.md).

> [!NOTE]
> Функция ускорения запросов доступна в общедоступной предварительной версии и доступна в центральных регионах для Канады и Франции. Чтобы просмотреть ограничения, см. статью [Известные проблемы](data-lake-storage-known-issues.md) . Чтобы зарегистрироваться в предварительной версии, см. [эту форму](https://aka.ms/adls/qa-preview-signup).  

## <a name="prerequisites"></a>Предварительные условия

### <a name="net"></a>[.NET](#tab/dotnet)

- Для доступа к службе хранилища Azure требуется подписка Azure. Если у вас еще нет подписки, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

- Учетная запись хранения **общего назначения версии 2** . см. раздел [Создание учетной записи хранения](../common/storage-quickstart-create-account.md).

- [пакет SDK для .NET](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- Для доступа к службе хранилища Azure требуется подписка Azure. Если у вас еще нет подписки, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

- Учетная запись хранения **общего назначения версии 2** . см. раздел [Создание учетной записи хранения](../common/storage-quickstart-create-account.md).

- [Пакет средств разработки Java (JDK)](/java/azure/jdk/?view=azure-java-stable) версии 8 или более поздней.

- [Apache Maven](https://maven.apache.org/download.cgi). 

  > [!NOTE] 
  > В этой статье предполагается, что вы создали проект Java с помощью Apache Maven. Пример создания проекта с помощью Apache Maven см. в разделе [Настройка](storage-quickstart-blobs-java.md#setting-up).
  
---

## <a name="install-packages"></a>Установка пакетов 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Скачайте пакеты ускорения запросов. Вы можете получить сжатый ZIP-файл, содержащий эти пакеты, используя следующую ссылку: [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net) . 

2. Извлеките содержимое этого файла в каталог проекта.

3. Откройте файл проекта (*. csproj*) в текстовом редакторе и добавьте эти ссылки на пакеты внутри \<Project\> элемента.

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Восстановите пакеты SDK предварительной версии. В этом примере команда восстанавливает предварительные версии пакетов SDK с помощью `dotnet restore` команды. 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Восстановите все остальные зависимости из общедоступного репозитория NuGet.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Создайте каталог в корне проекта. Корневой каталог — это каталог, содержащий файл **pom.xml** .

   > [!NOTE]
   > В примерах в этой статье предполагается, что имя каталога — **lib**.

2. Скачайте пакеты ускорения запросов. Вы можете получить сжатый ZIP-файл, содержащий эти пакеты, используя следующую ссылку: [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java) . 

3. Извлеките файлы из этого ZIP-файла в созданный вами каталог. В нашем примере этот каталог называется **lib**. 

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

Добавьте эти `using` инструкции в начало файла кода.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

Ускорение запросов извлекает данные в формате CSV и JSON. Поэтому обязательно добавьте операторы using для любых библиотек синтаксического анализа CSV или JSON, которые вы решили использовать. Примеры, представленные в этой статье, анализируют CSV-файл с помощью библиотеки [ксвхелпер](https://www.nuget.org/packages/CsvHelper/) , доступной в NuGet. Поэтому мы добавили эти `using` инструкции в начало файла кода.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Для компиляции примеров, представленных в этой статье, также необходимо добавить эти `using` инструкции.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Добавьте эти `import` инструкции в начало файла кода.

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

## <a name="retrieve-data-by-using-a-filter"></a>Получение данных с помощью фильтра

Вы можете использовать SQL для указания предикатов фильтра строк и проекций столбцов в запросе ускорения запроса. Следующий код запрашивает CSV-файл в хранилище и возвращает все строки данных, в которых третий столбец соответствует значению `Hemingway, Ernest` . 

- В SQL-запросе ключевое слово `BlobStorage` используется для обозначения файла, к которому выполняется запрос.

- Ссылки на столбцы указываются в том виде, в `_N` котором находится первый столбец `_1` . Если исходный файл содержит строку заголовка, то можно ссылаться на столбцы по имени, указанному в строке заголовка. 

### <a name="net"></a>[.NET](#tab/dotnet)

Асинхронный метод `BlobQuickQueryClient.QueryAsync` отправляет запрос в API ускорения запросов, а затем отправляет результаты обратно в приложение в виде объекта [потока](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8) .

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
        Console.Error.WriteLine($"Error: {err.Name}:{ err.Description }");
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

Метод `BlobQuickQueryClient.openInputStream()` отправляет запрос в API ускорения запросов, а затем передает результаты обратно в приложение как `InputStream` объект, который может быть считан как любой другой объект InputStream.

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

## <a name="retrieve-specific-columns"></a>Получение конкретных столбцов

Результаты можно ограничить подмножеством столбцов. Таким образом вы получаете только столбцы, необходимые для выполнения данного вычисления. Это повышает производительность приложения и сокращает затраты, поскольку по сети передается меньше данных. 

Этот код извлекает только `PublicationYear` столбец для всех книг в наборе данных. Он также использует сведения из строки заголовка исходного файла для ссылки на столбцы в запросе.


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

Следующий код сочетает фильтрацию строк и проекции столбцов в одном запросе. 

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

## <a name="next-steps"></a>Дальнейшие шаги

- [Форма регистрации ускорения запросов](https://aka.ms/adls/qa-preview-signup)    
- [Ускорение запросов Azure Data Lake Storage (Предварительная версия)](data-lake-storage-query-acceleration.md)
- [Справочник по языку SQL для ускорения запросов (Предварительная версия)](query-acceleration-sql-reference.md)
