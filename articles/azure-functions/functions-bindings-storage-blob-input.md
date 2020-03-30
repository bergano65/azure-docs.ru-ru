---
title: Привязка ввода хранилища Azure Blob для функций Azure
description: Узнайте, как предоставить данные хранения данных Azure Blob функции Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: e074d7d74c0c5f020cb8086124634b25012927db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77202155"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Привязка ввода хранилища Azure Blob для функций Azure

Привязка ввода позволяет читать данные о хранении каблов в качестве ввода функции Azure.

Для получения информации о настройке и деталях конфигурации, [см.](./functions-bindings-storage-blob.md)

## <a name="example"></a>Пример

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

<!--Same example for input and output. -->

В примере ниже показаны входная и выходная привязки больших двоичных объектов в файле *function.json*, а также код [скрипта C# (CSX)](functions-reference-csharp.md), который использует эти привязки. Функция копирует большой двоичный объект. Она активируется сообщением очереди, содержащим имя копируемого большого двоичного объекта. Новая капля называется *«originalblobname»-Copy*.

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

В разделе [Конфигурация](#configuration) описываются эти свойства.

Ниже приведен код скрипта C#.

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

<!--Same example for input and output. -->

В примере ниже показаны входная и выходная привязки BLOB-объектов в файле *function.json*, а также код [скрипта JavaScript](functions-reference-node.md), который использует эти привязки. Эта функция копирует большой двоичный объект. Она активируется сообщением очереди, содержащим имя копируемого большого двоичного объекта. Новая капля называется *«originalblobname»-Copy*.

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

В разделе [Конфигурация](#configuration) описываются эти свойства.

Ниже показан код JavaScript.

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

В приведенном ниже примере показаны входная и выходная привязки больших двоичных объектов в файле *function.json* и [код Python](functions-reference-python.md), который использует эти привязки. Эта функция копирует большой двоичный объект. Она активируется сообщением очереди, содержащим имя копируемого большого двоичного объекта. Новая капля называется *«originalblobname»-Copy*.

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

В разделе [Конфигурация](#configuration) описываются эти свойства.

Ниже приведен код Python.

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="java"></a>[Java](#tab/java)

Этот раздел содержит следующие примеры.

* [Триггер HTTP, поиск имени BLOB-объекта из строки запроса](#http-trigger-look-up-blob-name-from-query-string)
* [Очередь триггера, получение имени BLOB-объекта из сообщения очереди](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>Триггер HTTP, поиск имени BLOB-объекта из строки запроса

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

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Очередь триггера, получение имени BLOB-объекта из сообщения очереди

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

## <a name="attributes-and-annotations"></a>Атрибуты и аннотации

# <a name="c"></a>[C #](#tab/csharp)

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

Чтобы указать учетную запись хранения на уровне класса, метода или параметра, можно использовать атрибут `StorageAccount`. Для получения дополнительной информации [см.](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations)

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

Атрибуты не поддерживаются скриптом C'.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Атрибуты не поддерживаются JavaScript.

# <a name="python"></a>[Python](#tab/python)

Атрибуты не поддерживаются Python.

# <a name="java"></a>[Java](#tab/java)

Атрибут `@BlobInput` дает вам доступ к каплей, которая вызвала функцию. Если вы используете массив байт `dataType` с `binary`атрибутом, установите на . Для получения подробной информации обратитесь к [примеру ввода.](#example)

---

## <a name="configuration"></a>Параметр Configuration

В следующей таблице объясняется свойства связывающей конфигурации, `Blob` установленные в файле *function.json* и атрибуте.

|свойство function.json | Свойство атрибута |Описание|
|---------|---------|----------------------|
|**тип** | Недоступно | Нужно задать значение `blob`. |
|**direction** | Недоступно | Нужно задать значение `in`. Исключения приведены в этом [разделе](#usage). |
|**name** | Недоступно | Имя переменной, представляющей большой двоичный объект в коде функции.|
|**Путь** |**BlobPath** | Путь к BLOB-объекту. |
|**Подключения** |**Подключения**| Название параметра приложения, содержащего [строку подключения хранилища](../storage/common/storage-configure-connection-string.md) для использования для этого связывания. Если имя параметра приложения начинается с AzureWebJobs, можно указать только остальную часть имени. Например, если `connection` вы установите "MyStorage", время выполнения функций ищет настройки приложения, которые называются "AzureWebWebJobsMyStorage". Если оставить строку `connection` пустой, среда выполнения службы "Функции" будет использовать строку подключения к службе хранилища по умолчанию для параметра приложения с именем `AzureWebJobsStorage`.<br><br>Строка подключения необходима для учетной записи хранения общего назначения, а не [учетной записи хранения только для больших двоичных объектов](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|Недоступно | **Доступ** | Указывает, какая операция будет выполняться (запись или чтение). |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Использование

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[Скрипт C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

Доступ к данным `context.bindings.<NAME>` `<NAME>` blob, используя, где соответствует значению, определенному в *function.json*.

# <a name="python"></a>[Python](#tab/python)

Доступ к данным капли через параметр, набранный как [Вхотострим.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python) Для получения подробной информации обратитесь к [примеру ввода.](#example)

# <a name="java"></a>[Java](#tab/java)

Атрибут `@BlobInput` дает вам доступ к каплей, которая вызвала функцию. Если вы используете массив байт `dataType` с `binary`атрибутом, установите на . Для получения подробной информации обратитесь к [примеру ввода.](#example)

---

## <a name="next-steps"></a>Дальнейшие действия

- [Выполнить функцию при изменении данных хранения каблики](./functions-bindings-storage-blob-trigger.md)
- [Запись данных о хранении каблов из функции](./functions-bindings-storage-blob-output.md)
