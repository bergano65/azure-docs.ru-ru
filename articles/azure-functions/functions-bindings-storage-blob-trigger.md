---
title: Триггер хранения Azure Blob для функций Azure
description: Узнайте, как запустить функцию Azure как изменения данных хранения данных Azure Blob.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 61fbaf37577efdab0b147d437ae78fc4df0764cb
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084963"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Триггер хранения Azure Blob для функций Azure

Триггер хранилища BLOB-объектов запускает функцию при обнаружении нового или обновленного большого двоичного объекта. Содержимое капли предоставляется в качестве [ввода в функцию.](./functions-bindings-storage-blob-input.md)

Триггер хранения Azure Blob требует учетной записи хранения общего назначения. Чтобы использовать учетную запись только с blob или если ваше приложение имеет специализированные потребности, просмотрите альтернативы использованию этого триггера.

Для получения информации о настройке и деталях конфигурации, [см.](./functions-bindings-storage-blob.md)

## <a name="alternatives"></a>Альтернативные варианты

### <a name="event-grid-trigger"></a>Триггер сетки событий

Триггер [Event Grid](functions-bindings-event-grid.md) также имеет встроенную поддержку событий [blob.](../storage/blobs/storage-blob-event-overview.md) Используйте службу "Сетка событий" вместо триггера хранилища BLOB-объектов для следующих сценариев:

- **Учетные записи только для хранения данных Blob:** [учетные записи только для хранения данных Blob](../storage/common/storage-account-overview.md#types-of-storage-accounts) поддерживаются для входных и выходных привязок, но не для триггеров blob.

- **Высокие**масштабы : Высокий масштаб может быть свободно определены как контейнеры, которые имеют более чем 100000 капли в них или хранения счетов, которые имеют более чем 100 капля обновления в секунду.

- **Минимизация задержки:** Если ваше приложение функции находится в плане потребления, может быть до 10-минутной задержки в обработке новых капли, если приложение функции простаивает. Чтобы избежать этой задержки, можно переключиться на план службы приложений с включенным решением Always On. Можно также использовать [триггер сетки событий](functions-bindings-event-grid.md) с вашей учетной записью хранилища BLOB-объектов. Пример см. в [руководстве по Сетке событий](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

Просмотрите размер изображения с помощью учебника [Event Grid,](../event-grid/resize-images-on-storage-blob-upload-event.md) например, в случае событийной сетки.

### <a name="queue-storage-trigger"></a>Триггер хранилища очередей

Другой подход к обработке капли заключается в том, чтобы писать сообщения очереди, которые соответствуют создаваемым или измененным каплям, а затем использовать [триггер хранения очереди](./functions-bindings-storage-queue.md) для начала обработки.

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

Для получения дополнительной `BlobTrigger` информации об атрибуте [см.](#attributes-and-annotations)

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

В следующем примере показана привязка триггера blob в файле и коде *function.json,* который использует привязку. Функция записывает журнал, когда капля добавляется `samples-workitems` или обновляется в [контейнере.](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)

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

В следующем примере показана привязка триггера большого двоичного объекта в файле *function.json* и [код Python](functions-reference-python.md), который использует привязку данных. Функция записывает журнал, когда капля добавляется `samples-workitems` или обновляется в [контейнере.](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)

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

Эта функция записывает журнал, когда капля `myblob` добавляется или обновляется в контейнере.

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

## <a name="attributes-and-annotations"></a>Атрибуты и аннотации

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

  Для полного примера см. [пример триггера](#example).

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

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Атрибуты не поддерживаются скриптом C'.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Атрибуты не поддерживаются JavaScript.

# <a name="python"></a>[Python](#tab/python)

Атрибуты не поддерживаются Python.

# <a name="java"></a>[Java](#tab/java)

Атрибут `@BlobTrigger` используется, чтобы дать вам доступ к капля, который вызвал функцию. Обратитесь к [примеру триггера](#example) для получения подробной информации.

---

## <a name="configuration"></a>Конфигурация

В следующей таблице объясняется свойства связывающей конфигурации, `BlobTrigger` установленные в файле *function.json* и атрибуте.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `blobTrigger`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**Направление** | Недоступно | Нужно задать значение `in`. Это свойство задается автоматически при создании триггера на портале Azure. Исключения приведены в этом [разделе](#usage). |
|**name** | Недоступно | Имя переменной, представляющей большой двоичный объект в коде функции. |
|**путь** | **BlobPath** |[Контейнер](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) для мониторинга.  Может быть [шаблоном имени большого двоичного объекта](#blob-name-patterns). |
|**Подключения** | **Соединение** | Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.<br><br>Строка подключения необходима для учетной записи хранения общего назначения, а не [учетной записи хранения больших двоичных объектов](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Использование

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Доступ к данным `context.bindings.<NAME>` `<NAME>` blob, используя, где соответствует значению, определенному в *function.json*.

# <a name="python"></a>[Python](#tab/python)

Доступ к данным капли через параметр, набранный как [Вхотострим.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python) Обратитесь к [примеру триггера](#example) для получения подробной информации.

# <a name="java"></a>[Java](#tab/java)

Атрибут `@BlobTrigger` используется, чтобы дать вам доступ к капля, который вызвал функцию. Обратитесь к [примеру триггера](#example) для получения подробной информации.

---

## <a name="blob-name-patterns"></a>Шаблоны названий Blob

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

Если капля названа `name` * {20140101}-soundfile.mp3,* переменное значение в коде функции *soundfile.mp3.*

## <a name="metadata"></a>Метаданные

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

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

* Срабатывает функция ("*&lt;название приложения функции>*. Функции. функция имя>", например: "MyFunctionApp.Functions.CopyBlob") * &lt; *
* имя контейнера;
* тип большого двоичного объекта (BlockBlob или PageBlob);
* имя большого двоичного объекта;
* ETag (идентификатор версии больших двоичных объектов, например 0x8D1DC6E70A277EF).

Чтобы выполнить принудительную повторную обработку большого двоичного объекта, удалите уведомление о получении этого большого двоичного объекта из контейнера *azure-webjobs-hosts* вручную. Хотя переработка может произойти не сразу, она гарантированно произойдет в более поздний момент времени. Для немедленной переработки можно обновить *каплю scaninfo* в *лазурных веб-хостов/blobscaninfo.* Любые капли с последней измененной `LatestScan` меткой времени после того, как свойство будет отсканировано снова.

## <a name="poison-blobs"></a>Ядовитые капли

При сбое функции триггера BLOB-объекта Функции Azure по умолчанию выполняют ее для этого BLOB-объекта еще 5 раз.

В случае сбоя после 5 попыток запуска Функции Azure добавляют сообщение в очередь службы хранилища с именем *webjobs-blobtrigger-poison*. Можно настроить максимальное количество попыток. Тот же параметр MaxDequeueCount используется для обработки подозрительных больших двоичных объектов и подозрительных сообщений очереди. Сообщением очереди для подозрительных больших двоичных объектов является объект JSON, содержащий следующие свойства:

* FunctionId (в * &lt;функции формата имя приложения>*. Функции. имя функции>) * &lt; *
* BlobType (BlockBlob или PageBlob);
* ContainerName;
* BlobName
* ETag (идентификатор версии BLOB-объектов, например 0x8D1DC6E70A277EF)

## <a name="concurrency-and-memory-usage"></a>Использование параллелии и памяти

Триггер больших двоичных объектов использует очередь для внутренних целей, поэтому максимальное число параллельных вызовов функции регулируется [конфигурацией очередей в host.json](functions-host-json.md#queues). При настройках по умолчанию параллелизм ограничен 24 вызовами. Это ограничение применяется отдельно к каждой функции, которая использует триггер больших двоичных объектов.

[План потребления](functions-scale.md#how-the-consumption-and-premium-plans-work) ограничивает функциональное приложение на одной виртуальной машине (VM) до 1,5 ГБ памяти. Память используется каждым параллельно выполняющимся экземпляром функции и самой средой выполнения службы "Функции". Если функция, запускаемая триггером больших двоичных объектов, загружает весь такой объект в память, максимальный объем памяти, используемый этой функцией только для больших двоичных объектов, составит 24 * максимальный размер такого объекта. Например, приложение-функция с тремя функциями, запускаемыми триггером больших двоичных объектов, с настройками по умолчанию будет иметь максимальную степень параллелизма на одну виртуальную машину, равную 3 * 24 = 72 вызова функций.

Функции JavaScript и Java загружают весь большой двоичный объект в память, а функции C# делают это в случае привязки к `string`, `Byte[]` или POCO.

## <a name="polling"></a>Опрос

Опрос работает как гибрид между проверкой журналов и периодическим сканированием контейнеров. Blobs сканируются группами по 10 000 одновременно с токеном продолжения, используемым между интервалами.

> [!WARNING]
> Кроме того, [журналы службы хранилища создаются по принципу лучшего из возможного](/rest/api/storageservices/About-Storage-Analytics-Logging), Регистрация всех событий не гарантируется. В некоторых случаях журналы могут пропускаться.
> 
> Если требуется повысить скорость или надежность обработки BLOB-объектов, рекомендуем создать [сообщение очереди](../storage/queues/storage-dotnet-how-to-use-queues.md) при создании BLOB-объекта. Затем для обработки BLOB-объекта используйте [триггер очереди](functions-bindings-storage-queue.md) вместо триггера BLOB-объекта. Другой вариант — использовать службу "Сетка событий". Дополнительные сведения см. в руководстве [Автоматическое изменение размера переданных изображений с помощью службы "Сетка событий"](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="next-steps"></a>Дальнейшие действия

- [Чтение данных о хранении капли при запуске функции](./functions-bindings-storage-blob-input.md)
- [Запись данных о хранении каблов из функции](./functions-bindings-storage-blob-output.md)
