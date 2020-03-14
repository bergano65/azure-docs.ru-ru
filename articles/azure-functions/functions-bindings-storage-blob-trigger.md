---
title: Триггер хранилища BLOB-объектов Azure для функций Azure
description: Узнайте, как выполнять функции Azure в качестве изменений данных хранилища BLOB-объектов Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 0cdff3ac6eb2faed0c0b6b8796fdb3b6b0411018
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277366"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Триггер хранилища BLOB-объектов Azure для функций Azure

Триггер хранилища BLOB-объектов запускает функцию при обнаружении нового или обновленного большого двоичного объекта. Содержимое большого двоичного объекта предоставляется [функции в качестве входных данных](./functions-bindings-storage-blob-input.md).

Для триггера хранилища BLOB-объектов Azure требуется учетная запись хранения общего назначения. Чтобы использовать учетную запись только для больших двоичных объектов или если приложение имеет особые потребности, проверьте альтернативные варианты использования этого триггера.

Дополнительные сведения об установке и сведениях о конфигурации см. в [обзоре](./functions-bindings-storage-blob.md).

## <a name="alternatives"></a>Альтернативные варианты

### <a name="event-grid-trigger"></a>Триггер сетки событий

Триггер службы " [Сетка событий](functions-bindings-event-grid.md) " также имеет встроенную поддержку [событий больших двоичных объектов](../storage/blobs/storage-blob-event-overview.md). Используйте службу "Сетка событий" вместо триггера хранилища BLOB-объектов для следующих сценариев:

- **Учетные записи хранения только для больших двоичных**объектов. [учетные записи хранения только больших двоичных](../storage/common/storage-account-overview.md#types-of-storage-accounts) объектов поддерживаются для входных и выходных привязок BLOB-объектов, но не для триггеров

- **Высокая масштабируемость**. Высокая масштабируемость может быть слабо определена как контейнеры с более чем 100 000 большими двоичными объектами в них или учетными записями хранения, которые содержат более 100 обновлений больших двоичных объектов в секунду.

- **Минимизация задержки**. Если приложение-функция находится в плане потребления, то при обработке новых больших двоичных объектов может воздержаться задержка в 10 минут, если приложение-функция прекратило работу. Чтобы избежать этой задержки, можно переключиться на план службы приложений с включенным решением Always On. Можно также использовать [триггер сетки событий](functions-bindings-event-grid.md) с вашей учетной записью хранилища BLOB-объектов. Пример см. в [руководстве по Сетке событий](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

См. Руководство по [изменению размера образа с помощью сетки событий](../event-grid/resize-images-on-storage-blob-upload-event.md) в примере сетки событий.

### <a name="queue-storage-trigger"></a>Триггер хранилища очередей

Другой подход к обработке больших двоичных объектов — запись сообщений очереди, соответствующих создаваемым или изменяемым BLOB-объектам, а затем использование [триггера хранилища очередей](./functions-bindings-storage-queue.md) для начала обработки.

## <a name="example"></a>Пример

# <a name="c"></a>[C#](#tab/csharp)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая добавляет запись в журнал при добавлении или обновлении большого двоичного объекта в контейнере `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Строка `{name}` в пути триггера большого двоичного объекта `samples-workitems/{name}` создает [выражение привязки](./functions-bindings-expressions-patterns.md), которое можно использовать в коде функции для получения доступа к имени файла большого двоичного объекта, запускающего триггер. Дополнительные сведения см. в разделе [Шаблоны имени большого двоичного объекта](#blob-name-patterns).

Дополнительные сведения об атрибуте `BlobTrigger` см. в разделе [атрибуты и заметки](#attributes-and-annotations).

# <a name="c-script"></a>[C#Индекса](#tab/csharp-script)

В следующем примере показана привязка триггера большого двоичного объекта в файле *Function. JSON* и код, использующий привязку. Функция записывает журнал при добавлении или обновлении большого двоичного объекта в [контейнере](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)`samples-workitems`.

Данные привязки в файле *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Строка `{name}` в пути триггера большого двоичного объекта `samples-workitems/{name}` создает [выражение привязки](./functions-bindings-expressions-patterns.md), которое можно использовать в коде функции для получения доступа к имени файла большого двоичного объекта, запускающего триггер. Дополнительные сведения см. в разделе [Шаблоны имени большого двоичного объекта](#blob-name-patterns).

Дополнительные сведения о свойствах файла *function.json* см. в разделе [Конфигурация](#configuration).

Ниже приведен код скрипта C#, который выполняет привязку к `Stream`.

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Ниже приведен код скрипта C#, который выполняет привязку к `CloudBlockBlob`.

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

В следующем примере показана привязка триггера большого двоичного объекта в файле *function.json* и [коде JavaScript](functions-reference-node.md), который использует привязку. Функция делает запись в журнал при добавлении или обновлении большого двоичного объекта в контейнере `samples-workitems`.

Ниже показан файл *function.json*.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Строка `{name}` в пути триггера большого двоичного объекта `samples-workitems/{name}` создает [выражение привязки](./functions-bindings-expressions-patterns.md), которое можно использовать в коде функции для получения доступа к имени файла большого двоичного объекта, запускающего триггер. Дополнительные сведения см. в разделе [Шаблоны имени большого двоичного объекта](#blob-name-patterns).

Дополнительные сведения о свойствах файла *function.json* см. в разделе [Конфигурация](#configuration).

Ниже показан код JavaScript.

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

В следующем примере показана привязка триггера большого двоичного объекта в файле *function.json* и [код Python](functions-reference-python.md), который использует привязку данных. Функция записывает журнал при добавлении или обновлении большого двоичного объекта в [контейнере](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)`samples-workitems`.

Ниже показан файл *function.json*.

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Строка `{name}` в пути триггера большого двоичного объекта `samples-workitems/{name}` создает [выражение привязки](./functions-bindings-expressions-patterns.md), которое можно использовать в коде функции для получения доступа к имени файла большого двоичного объекта, запускающего триггер. Дополнительные сведения см. в разделе [Шаблоны имени большого двоичного объекта](#blob-name-patterns).

Дополнительные сведения о свойствах файла *function.json* см. в разделе [Конфигурация](#configuration).

Ниже приведен код Python.

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="java"></a>[Java](#tab/java)

Эта функция записывает журнал при добавлении или обновлении большого двоичного объекта в контейнере `myblob`.

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```

---

## <a name="attributes-and-annotations"></a>Атрибуты и заметки

# <a name="c"></a>[C#](#tab/csharp)

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте следующие атрибуты для настройки триггера большого двоичного объекта:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Конструктор атрибута принимает строку пути, которая указывает на контейнер для просмотра, и при необходимости [шаблон имени большого двоичного объекта](#blob-name-patterns). Ниже приведен пример:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Полный пример см. в разделе [пример триггера](#example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Предоставляет еще один способ указать используемую учетную запись хранения. Конструктор принимает имя параметра приложения, содержащего строку подключения к службе хранилища. Атрибут может применяться на уровне класса, метода или параметра. Ниже показан пример уровня класса и метода.

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

Используемая учетная запись хранения определяется в следующем порядке:

* Свойство `BlobTrigger` атрибута `Connection`.
* Атрибут `StorageAccount`, примененный к тому же параметру, что и `BlobTrigger`.
* Атрибут `StorageAccount`, примененный к функции.
* Атрибут `StorageAccount`, примененный к классу.
* Учетная запись хранения по умолчанию для приложения-функции (параметр приложения AzureWebJobsStorage).

# <a name="c-script"></a>[C#Индекса](#tab/csharp-script)

Атрибуты не поддерживаются C# сценарием.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Атрибуты не поддерживаются в JavaScript.

# <a name="python"></a>[Python](#tab/python)

Атрибуты не поддерживаются в Python.

# <a name="java"></a>[Java](#tab/java)

Атрибут `@BlobTrigger` используется для предоставления доступа к большому двоичному объекту, запускающему функцию. Дополнительные сведения см. в [примере триггера](#example) .

---

## <a name="configuration"></a>Конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `BlobTrigger`.

|свойство function.json | Свойство атрибута |Description|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `blobTrigger`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Недоступно | Нужно задать значение `in`. Это свойство задается автоматически при создании триггера на портале Azure. Исключения приведены в этом [разделе](#usage). |
|**name** | Недоступно | Имя переменной, представляющей большой двоичный объект в коде функции. |
|**путь** | **BlobPath** |[Контейнер](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) для мониторинга.  Может быть [шаблоном имени большого двоичного объекта](#blob-name-patterns). |
|**connection** | **Соединение** | Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.<br><br>Строка подключения необходима для учетной записи хранения общего назначения, а не [учетной записи хранения больших двоичных объектов](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Использование

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[C#Индекса](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Получите доступ к данным большого двоичного объекта с помощью `context.bindings.<NAME>`, где `<NAME>` соответствует значению, определенному в *Function. JSON*.

# <a name="python"></a>[Python](#tab/python)

Доступ к данным большого двоичного объекта с помощью параметра, типизированного как [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Дополнительные сведения см. в [примере триггера](#example) .

# <a name="java"></a>[Java](#tab/java)

Атрибут `@BlobTrigger` используется для предоставления доступа к большому двоичному объекту, запускающему функцию. Дополнительные сведения см. в [примере триггера](#example) .

---

## <a name="blob-name-patterns"></a>Шаблоны имен больших двоичных объектов

Вы можете указать шаблон имени большого двоичного объекта в свойстве `path` в файле *function.json* или в конструкторе атрибута `BlobTrigger`. Шаблон имени может быть [выражением фильтра или привязки](./functions-bindings-expressions-patterns.md). Примеры приведены в следующих разделах.

### <a name="get-file-name-and-extension"></a>Получение имени и расширения файла

В следующем примере показано, как выполнить привязку имени и расширения файла большого двоичного объекта по отдельности:

```json
"path": "input/{blobname}.{blobextension}",
```

Если большой двоичный объект имеет имя *original-Blob1.txt*, он передает в код функции переменные `blobname` и `blobextension` со значениями *original-Blob1* и *txt* соответственно.

### <a name="filter-on-blob-name"></a>Фильтрация по имени большого двоичного объекта

Следующий пример активируется только для больших двоичных объектов в контейнере `input`, который начинается со строки original-.

```json
"path": "input/original-{name}",
```

Если *original-Blob1.txt* — имя большого двоичного объекта, значением переменной `name` в коде функции будет `Blob1`.

### <a name="filter-on-file-type"></a>Фильтрация по типу файла

Далее приведены примеры триггеров только из файлов *PNG*:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Фильтрация по фигурным скобкам в именах файлов

Чтобы найти фигурные скобки в именах файлов, экранируйте скобки, используя две фигурные скобки. В следующем примере показана фильтрация по большим двоичным объектам, имена которых содержат фигурные скобки:

```json
"path": "images/{{20140101}}-{name}",
```

Если *{20140101}-soundfile.mp3* — имя большого двоичного объекта, значением переменной `name` в коде функции будет *soundfile.mp3*.

## <a name="metadata"></a>Метаданные

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[C#Индекса](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Метаданные недоступны в Python.

# <a name="java"></a>[Java](#tab/java)

Метаданные недоступны в Java.

---

## <a name="blob-receipts"></a>Уведомления о получении большого двоичного объекта

Среда выполнения Функций Azure гарантирует, что для одного и того же нового или обновленного BLOB-объекта функция активации BLOB-объекта будет вызываться только один раз. Для определения того, обработана ли версия этого BLOB-объекта, сохраняются *уведомления о получении BLOB-объекта*.

Функции Azure сохраняют уведомления о получении BLOB-объектов в контейнере с именем *azure-webjobs-hosts* в учетной записи хранения Azure для приложения-функции (указывается с помощью параметра приложения `AzureWebJobsStorage`). Уведомление о получении большого двоичного объекта содержит следующую информацию:

* активированная функция ( *&lt;имя_приложения-функции>* .Functions. *&lt;имя_функции>* , например: MyFunctionApp.Functions.CopyBlob);
* имя контейнера;
* тип большого двоичного объекта (BlockBlob или PageBlob);
* имя большого двоичного объекта;
* ETag (идентификатор версии больших двоичных объектов, например 0x8D1DC6E70A277EF).

Чтобы выполнить принудительную повторную обработку большого двоичного объекта, удалите уведомление о получении этого большого двоичного объекта из контейнера *azure-webjobs-hosts* вручную. Хотя повторная обработка может не выполняться немедленно, она гарантированно будет выполняться в более поздний момент времени.

## <a name="poison-blobs"></a>Подозрительные BLOB-объекты

При сбое функции триггера BLOB-объекта Функции Azure по умолчанию выполняют ее для этого BLOB-объекта еще 5 раз.

В случае сбоя после 5 попыток запуска Функции Azure добавляют сообщение в очередь службы хранилища с именем *webjobs-blobtrigger-poison*. Сообщением очереди для подозрительных больших двоичных объектов является объект JSON, содержащий следующие свойства:

* FunctionId (идентификатор функции в формате *&lt;имя_приложения-функции>* .Functions. *&lt;имя_функции>* );
* BlobType (BlockBlob или PageBlob);
* ContainerName;
* BlobName
* ETag (идентификатор версии BLOB-объектов, например 0x8D1DC6E70A277EF)

## <a name="concurrency-and-memory-usage"></a>Параллелизм и использование памяти

Триггер больших двоичных объектов использует очередь для внутренних целей, поэтому максимальное число параллельных вызовов функции регулируется [конфигурацией очередей в host.json](functions-host-json.md#queues). При настройках по умолчанию параллелизм ограничен 24 вызовами. Это ограничение применяется отдельно к каждой функции, которая использует триггер больших двоичных объектов.

[План потребления](functions-scale.md#how-the-consumption-and-premium-plans-work) ограничивает приложение-функцию на одной виртуальной машине (ВМ) до 1,5 ГБ памяти. Память используется каждым параллельно выполняющимся экземпляром функции и самой средой выполнения службы "Функции". Если функция, запускаемая триггером больших двоичных объектов, загружает весь такой объект в память, максимальный объем памяти, используемый этой функцией только для больших двоичных объектов, составит 24 * максимальный размер такого объекта. Например, приложение-функция с тремя функциями, запускаемыми триггером больших двоичных объектов, с настройками по умолчанию будет иметь максимальную степень параллелизма на одну виртуальную машину, равную 3 * 24 = 72 вызова функций.

Функции JavaScript и Java загружают весь большой двоичный объект в память, а функции C# делают это в случае привязки к `string`, `Byte[]` или POCO.

## <a name="polling"></a>Опроса

Опрос работает как гибрид между проверками журналов и выполнения периодических просмотров контейнеров. Большие двоичные объекты проверяются в группах 10 000 за раз с токеном продолжения, используемым между интервалами.

> [!WARNING]
> Кроме того, [журналы службы хранилища создаются по принципу лучшего из возможного](/rest/api/storageservices/About-Storage-Analytics-Logging), Регистрация всех событий не гарантируется. В некоторых случаях журналы могут пропускаться.
> 
> Если требуется повысить скорость или надежность обработки BLOB-объектов, при создании BLOB-объекта рекомендуем создать [сообщение очереди](../storage/queues/storage-dotnet-how-to-use-queues.md). Затем для обработки BLOB-объекта используйте [триггер очереди](functions-bindings-storage-queue.md) вместо триггера BLOB-объекта. Другой вариант — использовать службу "Сетка событий". Дополнительные сведения см. в руководстве [Автоматическое изменение размера переданных изображений с помощью службы "Сетка событий"](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="next-steps"></a>Дальнейшие действия

- [Чтение данных хранилища BLOB-объектов при выполнении функции](./functions-bindings-storage-blob-input.md)
- [Запись данных хранилища BLOB-объектов из функции](./functions-bindings-storage-blob-output.md)
