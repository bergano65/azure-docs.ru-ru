---
title: Привязки хранилища BLOB-объектов Azure для службы "Функции Azure"
description: Узнайте, как использовать триггеры и привязки хранилища BLOB-объектов Azure в службе "Функции Azure".
author: craigshoemaker
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: b2782ce39bbc2ca86c63b178535fc6b67b9dadfe
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231035"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Привязки хранилища BLOB-объектов Azure для службы "Функции Azure"

В этой статье рассматривается работа с привязками хранилища BLOB-объектов Azure в службе "Функции Azure". Служба "Функции Azure" поддерживает привязки триггера, а также входные и выходные привязки для больших двоичных объектов. В этой статье каждой из следующих привязок посвящен раздел:

* [триггер большого двоичного объекта](#trigger);
* [входная привязка большого двоичного объекта](#input);
* [выходная привязка большого двоичного объекта](#output).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Используйте триггер службы "Сетка событий" вместо триггера хранилища BLOB-объектов для учетных записей хранения только для больших двоичных объектов, чтобы обеспечить высокий уровень масштабирования или сократить задержку. Дополнительные сведения см. в разделе [Триггер](#trigger).

## <a name="packages---functions-1x"></a>Пакеты — Функции 1.x

Привязки большого двоичного объекта доступны в пакете NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), версия 2.х. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Пакеты — Функции 2.x

Привязки хранилища BLOB-объектов доступны в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) версии 3.х. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Триггер

Триггер хранилища BLOB-объектов запускает функцию при обнаружении нового или обновленного большого двоичного объекта. Содержимое BLOB-объекта предоставляется в качестве входных данных функции.

[Триггер службы "Сетка событий"](functions-bindings-event-grid.md) имеет встроенную поддержку [событий больших двоичных объектов](../storage/blobs/storage-blob-event-overview.md). Кроме того, его можно использовать для запуска функции при обнаружении нового или обновленного большого двоичного объекта. Пример см. в руководстве [Автоматическое изменение размера переданных изображений с помощью службы "Сетка событий"](../event-grid/resize-images-on-storage-blob-upload-event.md).

Используйте службу "Сетка событий" вместо триггера хранилища BLOB-объектов для следующих сценариев:

* Учетные записи хранения BLOB-объектов
* Большой масштаб
* Минимизация задержки

### <a name="blob-storage-accounts"></a>Учетные записи хранения BLOB-объектов

[Учетные записи хранения больших двоичных объектов](../storage/common/storage-account-overview.md#types-of-storage-accounts) поддерживаются для входных и выходных привязок больших двоичных объектов, а не для триггеров большого двоичного объекта. Для триггеров хранилища BLOB-объектов требуется учетная запись хранения общего назначения.

### <a name="high-scale"></a>Большой масштаб

Большой масштаб можно определить как контейнеры, содержащие более 100 000 больших двоичных объектов, или как учетные записи хранения, в которых выполняется более 100 обновлений больших двоичных объектов в секунду.

### <a name="latency-issues"></a>Проблемы с задержкой

Если ваше приложение-функция выполняется в рамках плана потребления, обработка новых больших двоичных объектов может осуществляться с задержкой до 10 минут, если приложение-функция стало неактивным. Чтобы избежать этой задержки, можно переключиться на план службы приложений с включенным решением Always On. Можно также использовать [триггер сетки событий](functions-bindings-event-grid.md) с вашей учетной записью хранилища BLOB-объектов. Пример см. в [руководстве по Сетке событий](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Триггер хранилища очередей

Помимо службы "Сетка событий" для обработки больших двоичных объектов можно использовать триггер хранилища очередей, но он не имеет встроенной поддержки для событий большого двоичного объекта. При создании или обновлении больших двоичных объектов вам придется создавать сообщения очереди. Пример см. в [этом разделе](#input---example).

## <a name="trigger---example"></a>Пример триггера

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая добавляет запись в журнал при добавлении или обновлении большого двоичного объекта в контейнере `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Строка `{name}` в пути триггера большого двоичного объекта `samples-workitems/{name}` создает [выражение привязки](./functions-bindings-expressions-patterns.md), которое можно использовать в коде функции для получения доступа к имени файла большого двоичного объекта, запускающего триггер. Дополнительные сведения см. в разделе [Шаблоны имени большого двоичного объекта](#trigger---blob-name-patterns).

Дополнительные сведения об атрибуте `BlobTrigger` см. в разделе [Атрибуты триггера для предкомпилированного кода C#](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

В следующем примере показана привязка триггера большого двоичного объекта в файле *Function. JSON* и код, использующий привязку. Функция делает запись в журнал при добавлении или обновлении большого двоичного объекта в `samples-workitems`контейнере[ ](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

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

Строка `{name}` в пути триггера большого двоичного объекта `samples-workitems/{name}` создает [выражение привязки](./functions-bindings-expressions-patterns.md), которое можно использовать в коде функции для получения доступа к имени файла большого двоичного объекта, запускающего триггер. Дополнительные сведения см. в разделе [Шаблоны имени большого двоичного объекта](#trigger---blob-name-patterns).

Дополнительные сведения о свойствах файла *function.json* см. в разделе [Конфигурация](#trigger---configuration).

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

Строка `{name}` в пути триггера большого двоичного объекта `samples-workitems/{name}` создает [выражение привязки](./functions-bindings-expressions-patterns.md), которое можно использовать в коде функции для получения доступа к имени файла большого двоичного объекта, запускающего триггер. Дополнительные сведения см. в разделе [Шаблоны имени большого двоичного объекта](#trigger---blob-name-patterns).

Дополнительные сведения о свойствах файла *function.json* см. в разделе [Конфигурация](#trigger---configuration).

Ниже показан код JavaScript.

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

В следующем примере показана привязка триггера большого двоичного объекта в файле *function.json* и [код Python](functions-reference-python.md), который использует привязку данных. Функция делает запись в журнал при добавлении или обновлении большого двоичного объекта в `samples-workitems`контейнере[ ](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

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

Строка `{name}` в пути триггера большого двоичного объекта `samples-workitems/{name}` создает [выражение привязки](./functions-bindings-expressions-patterns.md), которое можно использовать в коде функции для получения доступа к имени файла большого двоичного объекта, запускающего триггер. Дополнительные сведения см. в разделе [Шаблоны имени большого двоичного объекта](#trigger---blob-name-patterns).

Дополнительные сведения о свойствах файла *function.json* см. в разделе [Конфигурация](#trigger---configuration).

Ниже приведен код Python.

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="javatabjava"></a>[Java](#tab/java)

В следующем примере показана привязка триггера большого двоичного объекта в файле *function.json* и [код Java](functions-reference-java.md), который использует привязку данных. Функция делает запись в журнал при добавлении или обновлении большого двоичного объекта в контейнере `myblob`.

Ниже показан файл *function.json*.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "file",
            "type": "blobTrigger",
            "direction": "in",
            "path": "myblob/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Ниже приведен код Java.

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

## <a name="trigger---attributes"></a>Атрибуты триггера

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте следующие атрибуты для настройки триггера большого двоичного объекта:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Конструктор атрибута принимает строку пути, которая указывает на контейнер для просмотра, и при необходимости [шаблон имени большого двоичного объекта](#trigger---blob-name-patterns). Ниже приведен пример:

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

  Полный пример см. в разделе [пример триггера](#trigger---example).

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

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

Атрибуты не поддерживаются C# сценарием.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Атрибуты не поддерживаются в JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Атрибуты не поддерживаются в Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Атрибут `@BlobTrigger` используется для предоставления доступа к большому двоичному объекту, запускающему функцию. Дополнительные сведения см. в [примере триггера](#trigger---example) .

---

## <a name="trigger---configuration"></a>Конфигурация триггера

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `BlobTrigger`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `blobTrigger`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Недоступно | Нужно задать значение `in`. Это свойство задается автоматически при создании триггера на портале Azure. Исключения приведены в этом [разделе](#trigger---usage). |
|**name** | Недоступно | Имя переменной, представляющей большой двоичный объект в коде функции. |
|**path** | **BlobPath** |[Контейнер](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) для мониторинга.  Может быть [шаблоном имени большого двоичного объекта](#trigger---blob-name-patterns). |
|**подключение** | **Connection** | Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.<br><br>Строка подключения необходима для учетной записи хранения общего назначения, а не [учетной записи хранения больших двоичных объектов](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Использование триггера

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Доступ к данным больших двоичных объектов с помощью `context.bindings.<name from function.json>`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Доступ к данным большого двоичного объекта с помощью параметра, типизированного как [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Дополнительные сведения см. в [примере триггера](#trigger---example) .

# <a name="javatabjava"></a>[Java](#tab/java)

Атрибут `@BlobTrigger` используется для предоставления доступа к большому двоичному объекту, запускающему функцию. Дополнительные сведения см. в [примере триггера](#trigger---example) .

---

## <a name="trigger---blob-name-patterns"></a>Шаблоны имени большого двоичного объекта в триггере

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

## <a name="trigger---metadata"></a>Метаданные триггера

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Метаданные недоступны в Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Метаданные недоступны в Java.

---

## <a name="trigger---blob-receipts"></a>Уведомления о получении большого двоичного объекта в триггере

Среда выполнения Функций Azure гарантирует, что для одного и того же нового или обновленного BLOB-объекта функция активации BLOB-объекта будет вызываться только один раз. Для определения того, обработана ли версия этого BLOB-объекта, сохраняются *уведомления о получении BLOB-объекта*.

Функции Azure сохраняют уведомления о получении BLOB-объектов в контейнере с именем *azure-webjobs-hosts* в учетной записи хранения Azure для приложения-функции (указывается с помощью параметра приложения `AzureWebJobsStorage`). Уведомление о получении большого двоичного объекта содержит следующую информацию:

* активированная функция ( *&lt;имя_приложения-функции>* .Functions. *&lt;имя_функции>* , например: MyFunctionApp.Functions.CopyBlob);
* имя контейнера;
* тип большого двоичного объекта (BlockBlob или PageBlob);
* имя большого двоичного объекта;
* ETag (идентификатор версии больших двоичных объектов, например 0x8D1DC6E70A277EF).

Чтобы выполнить принудительную повторную обработку большого двоичного объекта, удалите уведомление о получении этого большого двоичного объекта из контейнера *azure-webjobs-hosts* вручную. Хотя повторная обработка может не выполняться немедленно, она гарантированно будет выполняться в более поздний момент времени.

## <a name="trigger---poison-blobs"></a>Триггеры подозрительных больших двоичных объектов

При сбое функции триггера BLOB-объекта Функции Azure по умолчанию выполняют ее для этого BLOB-объекта еще 5 раз.

В случае сбоя после 5 попыток запуска Функции Azure добавляют сообщение в очередь службы хранилища с именем *webjobs-blobtrigger-poison*. Сообщением очереди для подозрительных больших двоичных объектов является объект JSON, содержащий следующие свойства:

* FunctionId (идентификатор функции в формате *&lt;имя_приложения-функции>* .Functions. *&lt;имя_функции>* );
* BlobType (BlockBlob или PageBlob);
* ContainerName;
* BlobName
* ETag (идентификатор версии BLOB-объектов, например 0x8D1DC6E70A277EF)

## <a name="trigger---concurrency-and-memory-usage"></a>Триггеры параллелизма и использования памяти

Триггер больших двоичных объектов использует очередь для внутренних целей, поэтому максимальное число параллельных вызовов функции регулируется [конфигурацией очередей в host.json](functions-host-json.md#queues). При настройках по умолчанию параллелизм ограничен 24 вызовами. Это ограничение применяется отдельно к каждой функции, которая использует триггер больших двоичных объектов.

[План потребления](functions-scale.md#how-the-consumption-and-premium-plans-work) ограничивает для приложения-функции на одной виртуальной машине объем памяти в 1,5 ГБ. Память используется каждым параллельно выполняющимся экземпляром функции и самой средой выполнения службы "Функции". Если функция, запускаемая триггером больших двоичных объектов, загружает весь такой объект в память, максимальный объем памяти, используемый этой функцией только для больших двоичных объектов, составит 24 * максимальный размер такого объекта. Например, приложение-функция с тремя функциями, запускаемыми триггером больших двоичных объектов, с настройками по умолчанию будет иметь максимальную степень параллелизма на одну виртуальную машину, равную 3 * 24 = 72 вызова функций.

Функции JavaScript и Java загружают весь большой двоичный объект в память, а функции C# делают это в случае привязки к `string`, `Byte[]` или POCO.

## <a name="trigger---polling"></a>Триггер опроса

Если отслеживаемый контейнер больших двоичных объектов содержит более 10 000 больших двоичных объектов (во всех контейнерах), среда выполнения функций сканирует файлы журнала для отслеживания новых или измененных больших двоичных объектов. Этот процесс может привести к задержкам. После создания большого двоичного объекта функция может не вызываться несколько минут или даже дольше.

> [!WARNING]
> Кроме того, [журналы службы хранилища создаются по принципу лучшего из возможного](/rest/api/storageservices/About-Storage-Analytics-Logging), Регистрация всех событий не гарантируется. В некоторых случаях журналы могут пропускаться.
> 
> Если требуется повысить скорость или надежность обработки BLOB-объектов, при создании BLOB-объекта рекомендуем создать [сообщение очереди](../storage/queues/storage-dotnet-how-to-use-queues.md). Затем для обработки BLOB-объекта используйте [триггер очереди](functions-bindings-storage-queue.md) вместо триггера BLOB-объекта. Другой вариант — использовать службу "Сетка событий". Дополнительные сведения см. в руководстве [Автоматическое изменение размера переданных изображений с помощью службы "Сетка событий"](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="input"></a>Входные данные

Для чтения больших двоичных объектов используйте входную привязку хранилища BLOB-объектов.

## <a name="input---example"></a>Пример входных данных

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Ниже приведен пример [функции C#](functions-dotnet-class-library.md), которая использует триггер очереди и входную привязку большого двоичного объекта. Сообщение в очереди содержит имя большого двоичного объекта, а функция записывает в журнал размер большого двоичного объекта.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

<!--Same example for input and output. -->

В примере ниже показаны входная и выходная привязки больших двоичных объектов в файле *function.json*, а также код [скрипта C# (CSX)](functions-reference-csharp.md), который использует эти привязки. Функция копирует большой двоичный объект. Она активируется сообщением очереди, содержащим имя копируемого большого двоичного объекта. Новый большой двоичный объект получает имя *{originalblobname}-Copy*.

В файле *function.json* свойство метаданных `queueTrigger` используется для указания имени большого двоичного объекта в свойствах `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

В примере ниже показаны входная и выходная привязки BLOB-объектов в файле *function.json*, а также код [скрипта JavaScript](functions-reference-node.md), который использует эти привязки. Эта функция копирует большой двоичный объект. Она активируется сообщением очереди, содержащим имя копируемого большого двоичного объекта. Новый большой двоичный объект получает имя *{originalblobname}-Copy*.

В файле *function.json* свойство метаданных `queueTrigger` используется для указания имени большого двоичного объекта в свойствах `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

<!--Same example for input and output. -->

В приведенном ниже примере показаны входная и выходная привязки больших двоичных объектов в файле *function.json* и [код Python](functions-reference-python.md), который использует эти привязки. Эта функция копирует большой двоичный объект. Она активируется сообщением очереди, содержащим имя копируемого большого двоичного объекта. Новый большой двоичный объект получает имя *{originalblobname}-Copy*.

В файле *function.json* свойство метаданных `queueTrigger` используется для указания имени большого двоичного объекта в свойствах `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

В разделе [Конфигурация](#input---configuration) описываются эти свойства.

Ниже приведен код Python.

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="javatabjava"></a>[Java](#tab/java)

В этом разделе содержатся следующие примеры:

* [Триггер HTTP, поиск имени BLOB-объекта из строки запроса](#http-trigger-look-up-blob-name-from-query-string)
* [Очередь триггера, получение имени BLOB-объекта из сообщения очереди](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>Триггер HTTP, поиск имени большого двоичного объекта из строки запроса

 В следующем примере показана функция Java, которая использует заметку `HttpTrigger` для получения параметра, содержащего имя файла в контейнере хранилища BLOB-объектов. После этого заметка `BlobInput` считывает файл и передает его содержимое в функцию как `byte[]`.

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Триггер очереди, получение имени большого двоичного объекта из сообщения очереди

 В следующем примере показана функция Java, которая использует заметку `QueueTrigger` для получения сообщения, содержащего имя файла в контейнере хранилища BLOB-объектов. После этого заметка `BlobInput` считывает файл и передает его содержимое в функцию как `byte[]`.

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

В [библиотеке среды выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `@BlobInput` для параметров, значение которых будут поступать из большого двоичного объекта.  Эта заметка может использоваться с собственными типами Java, объектами POJO или значениями, допускающими значения NULL, используя `Optional<T>`.

---

## <a name="input---attributes"></a>Входные атрибуты

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Конструктор атрибута принимает путь к большому двоичному объекту и параметр `FileAccess`, указывающий на чтение или запись, как показано в следующем примере:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Чтобы указать учетную запись хранения на уровне класса, метода или параметра, можно использовать атрибут `StorageAccount`. Дополнительные сведения см. в разделе [Атрибуты триггера для предкомпилированного кода C#](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

Атрибуты не поддерживаются C# сценарием.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Атрибуты не поддерживаются в JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Атрибуты не поддерживаются в Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Атрибут `@BlobInput` предоставляет доступ к большому двоичному объекту, вызвавшему функцию. Если используется массив байтов с атрибутом, задайте для `dataType` значение `binary`. Дополнительные сведения см. в [примере входного данных](#input---example) .

---

## <a name="input---configuration"></a>Входная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Blob`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `blob`. |
|**direction** | Недоступно | Нужно задать значение `in`. Исключения приведены в этом [разделе](#input---usage). |
|**name** | Недоступно | Имя переменной, представляющей большой двоичный объект в коде функции.|
|**path** |**BlobPath** | Путь к BLOB-объекту. |
|**подключение** |**Connection**| Имя параметра приложения, содержащего [строку подключения к службе хранилища](../storage/common/storage-configure-connection-string.md), используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.<br><br>Строка подключения необходима для учетной записи хранения общего назначения, а не [учетной записи хранения только для больших двоичных объектов](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Недоступно | **Access** | Указывает, какая операция будет выполняться (запись или чтение). |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Использование входной привязки

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Получите доступ к данным больших двоичных объектов с помощью `context.bindings.<name from function.json>`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Доступ к данным большого двоичного объекта с помощью параметра, типизированного как [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Дополнительные сведения см. в [примере входного данных](#input---example) .

# <a name="javatabjava"></a>[Java](#tab/java)

Атрибут `@BlobInput` предоставляет доступ к большому двоичному объекту, вызвавшему функцию. Если используется массив байтов с атрибутом, задайте для `dataType` значение `binary`. Дополнительные сведения см. в [примере входного данных](#input---example) .

---

## <a name="output"></a>Выходные данные

Используйте выходные привязки хранилища BLOB-объектов для записи больших двоичных объектов.

## <a name="output---example"></a>Пример выходных данных

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Ниже приведен пример [функции C#](functions-dotnet-class-library.md), которая использует триггер большого двоичного объекта и две выходные привязки больших двоичных объектов. Эта функция активируется путем создания большого двоичного объекта образа в контейнере *sample-images*. Она создает небольшие и средние копии большого двоичного объекта образа.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    IImageFormat format;

    using (Image<Rgba32> input = Image.Load(image, out format))
    {
      ResizeImage(input, imageSmall, ImageSize.Small, format);
    }

    image.Position = 0;
    using (Image<Rgba32> input = Image.Load(image, out format))
    {
      ResizeImage(input, imageMedium, ImageSize.Medium, format);
    }
}

public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
{
    var dimensions = imageDimensionsTable[size];

    input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
    input.Save(output, format);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

<!--Same example for input and output. -->

В примере ниже показаны входная и выходная привязки больших двоичных объектов в файле *function.json*, а также код [скрипта C# (CSX)](functions-reference-csharp.md), который использует эти привязки. Функция копирует большой двоичный объект. Она активируется сообщением очереди, содержащим имя копируемого большого двоичного объекта. Новый большой двоичный объект получает имя *{originalblobname}-Copy*.

В файле *function.json* свойство метаданных `queueTrigger` используется для указания имени большого двоичного объекта в свойствах `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

В примере ниже показаны входная и выходная привязки BLOB-объектов в файле *function.json*, а также код [скрипта JavaScript](functions-reference-node.md), который использует эти привязки. Эта функция копирует большой двоичный объект. Она активируется сообщением очереди, содержащим имя копируемого большого двоичного объекта. Новый большой двоичный объект получает имя *{originalblobname}-Copy*.

В файле *function.json* свойство метаданных `queueTrigger` используется для указания имени большого двоичного объекта в свойствах `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

<!--Same example for input and output. -->

В приведенном ниже примере показаны входная и выходная привязки больших двоичных объектов в файле *function.json* и [код Python](functions-reference-python.md), который использует эти привязки. Эта функция копирует большой двоичный объект. Она активируется сообщением очереди, содержащим имя копируемого большого двоичного объекта. Новый большой двоичный объект получает имя *{originalblobname}-Copy*.

В файле *function.json* свойство метаданных `queueTrigger` используется для указания имени большого двоичного объекта в свойствах `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

В разделе [Конфигурация](#output---configuration) описываются эти свойства.

Ниже приведен код Python.

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="javatabjava"></a>[Java](#tab/java)

В этом разделе содержатся следующие примеры:

* [Триггер HTTP, использование OutputBinding](#http-trigger-using-outputbinding-java)
* [Триггер очереди, использование возвращаемого значения функции](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Триггер HTTP, использование OutputBinding (Java)

 В следующем примере показана функция Java, которая использует заметку `HttpTrigger` для получения параметра, содержащего имя файла в контейнере хранилища BLOB-объектов. После этого заметка `BlobInput` считывает файл и передает его содержимое в функцию как `byte[]`. Заметка `BlobOutput` выполняет привязку к `OutputBinding outputItem`, которая затем используется функцией для записи содержимого входного BLOB-объекта в контейнер настраиваемого хранилища.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Триггер очереди, использование возвращаемого значения функции (Java)

 В следующем примере показана функция Java, которая использует заметку `QueueTrigger` для получения сообщения, содержащего имя файла в контейнере хранилища BLOB-объектов. После этого заметка `BlobInput` считывает файл и передает его содержимое в функцию как `byte[]`. Заметка `BlobOutput` выполняет привязку к возвращаемому значению функции, которая затем используется средой выполнения для записи содержимого входного BLOB-объекта в контейнер настраиваемого хранилища.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 В [библиотеке среды выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `@BlobOutput` для параметров функции, значение которых будут записываться в хранилище больших двоичных объектов.  Параметр должен быть типа `OutputBinding<T>`, где T — любой собственный тип Java или POJO.

---

## <a name="output---attributes"></a>Выходные атрибуты

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Конструктор атрибута принимает путь к большому двоичному объекту и параметр `FileAccess`, указывающий на чтение или запись, как показано в следующем примере:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Чтобы указать учетную запись хранения, которую нужно использовать, можно задать свойство `Connection`, как показано в следующем примере.

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

Атрибуты не поддерживаются C# сценарием.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Атрибуты не поддерживаются в JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Атрибуты не поддерживаются в Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Атрибут `@BlobOutput` предоставляет доступ к большому двоичному объекту, вызвавшему функцию. Если используется массив байтов с атрибутом, задайте для `dataType` значение `binary`. Дополнительные сведения см. в [примере выходных данных](#output---example) .

---

Полный пример см. в разделе [пример выходных данных](#output---example).

Чтобы указать учетную запись хранения на уровне класса, метода или параметра, можно использовать атрибут `StorageAccount`. Дополнительные сведения см. в разделе [Атрибуты триггера для предкомпилированного кода C#](#trigger---attributes).

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Blob`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `blob`. |
|**direction** | Недоступно | Нужно задать значение `out` для выходной привязки. Исключения приведены в этом [разделе](#output---usage). |
|**name** | Недоступно | Имя переменной, представляющей большой двоичный объект в коде функции.  Задайте значение `$return`, ссылающееся на возвращаемое значение функции.|
|**path** |**BlobPath** | Путь к контейнеру больших двоичных объектов. |
|**подключение** |**Connection**| Имя параметра приложения, содержащего строку подключения к службе хранилища, используемой для этой привязки. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если задать для `connection` значение MyStorage, среда выполнения службы "Функции" будет искать параметр приложения с именем AzureWebJobsMyStorage. Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.<br><br>Строка подключения необходима для учетной записи хранения общего назначения, а не [учетной записи хранения только для больших двоичных объектов](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Недоступно | **Access** | Указывает, какая операция будет выполняться (запись или чтение). |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Использование выходной привязки

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Индекса](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

В JavaScript получите доступ к данным больших двоичных объектов с помощью `context.bindings.<name from function.json>`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Параметры функции можно объявить как следующие типы для записи в хранилище BLOB-объектов:

* Строки как `func.Out(str)`
* Потоки как `func.Out(func.InputStream)`

Дополнительные сведения см. в [примере выходных данных](#output---example) .

# <a name="javatabjava"></a>[Java](#tab/java)

Атрибут `@BlobOutput` предоставляет доступ к большому двоичному объекту, вызвавшему функцию. Если используется массив байтов с атрибутом, задайте для `dataType` значение `binary`. Дополнительные сведения см. в [примере выходных данных](#output---example) .

---

## <a name="exceptions-and-return-codes"></a>Коды возврата и исключений

| Привязка |  Справочные материалы |
|---|---|
| BLOB-объект | [Коды ошибок больших двоичных объектов](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Большой двоичный объект, таблица, очередь |  [Коды ошибок хранилища](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Большой двоичный объект, таблица, очередь |  [Устранение неполадок](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Дополнительная информация

* [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
