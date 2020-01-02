---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: c505909599b6b69719de1cb9224db52d2f524b2b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935920"
---
## <a name="local-settings-file"></a>Файл с локальными параметрами

В файле Local. Settings. JSON хранятся параметры приложения, строки подключения и параметры, используемые локальными инструментами разработки. Параметры в файле Local. Settings. JSON используются только при локальном запуске проектов. Файл локальных параметров имеет следующую структуру:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

Эти параметры поддерживаются при локальном запуске проектов.

| Параметр      | Описание                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Если этот параметр имеет значение `true`, все значения шифруются с помощью ключа локального компьютера. Используется с командами `func settings`. По умолчанию имеет значение `false`. |
| **`Values`** | Массив параметров приложения и строк подключения, используемых при локальном запуске проекта. Эти пары "ключ — значение" (строковые строки) соответствуют параметрам приложения в приложении-функции в Azure, например [`AzureWebJobsStorage`]. Многие триггеры и привязки имеют свойство, которое ссылается на параметр приложения строки подключения, например `Connection` для [триггера хранилища BLOB-объектов](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration). Для этих свойств необходим параметр приложения, определенный в массиве `Values`. <br/>[`AzureWebJobsStorage`] является обязательным параметром приложения для триггеров, отличных от HTTP. <br/>Для версии 2. x и более поздней версии среды выполнения функций требуется параметр [`FUNCTIONS_WORKER_RUNTIME`], который создается для проекта основными инструментами. <br/> Если [эмулятор хранения Azure](../articles/storage/common/storage-use-emulator.md) установлен локально и для [`AzureWebJobsStorage`] задано значение `UseDevelopmentStorage=true`, основные инструменты используют эмулятор. Эмулятор полезен во время разработки, но перед развертыванием следует проверить фактическое подключение к хранилищу.<br/> Значения должны быть строками, а не объектами JSON или массивами. Имена параметров не могут содержать двоеточие (`:`) или двойное подчеркивание (`__`). Эти символы зарезервированы средой выполнения.  |
| **`Host`** | Параметры в этом разделе позволяют настроить хостный процесс функций при локальном запуске проектов. Эти параметры отделены от параметров Host. JSON, которые также применяются при запуске проектов в Azure. |
| **`LocalHttpPort`** | Задает порт по умолчанию, используемый при выполнении локального узла Функций (`func host start` и `func run`). Параметр командной строки `--port` имеет приоритет над этим параметром. |
| **`CORS`** | Определяет источники, для которых разрешен [общий доступ к ресурсам независимо от источника (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Источники указываются в виде разделенного запятыми списка без пробелов. Допускается подстановочное значение (\*), разрешающее запросы из любого источника. |
| **`CORSCredentials`** |  Если задано значение `true`, разрешает запросы `withCredentials`. |
| **`ConnectionStrings`** | Коллекция. Не используйте эту коллекцию для строк подключения, используемых привязками функций. Эта коллекция используется только платформами, которые обычно получают строки подключения из `ConnectionStrings` раздела файла конфигурации, например [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Строки подключения, содержащиеся в этом объекте, добавляются в среду с типом поставщика [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Элементы в этой коллекции не публикуются в Azure с другими параметрами приложения. Эти значения необходимо явно добавить в коллекцию `Connection strings` параметров приложения функции. При создании [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) в коде функции необходимо сохранить значение строки подключения с другими подключениями в **параметрах приложения** на портале. |

[AzureWebJobsStorage]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
