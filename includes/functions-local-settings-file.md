---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: e319356d555f26354ea29dc7be068bf6168abb17
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455147"
---
## <a name="local-settings-file"></a>Файл с локальными параметрами

Файл local.settings.json хранит параметры приложения, строки подключения и параметры, используемые средствами локальной разработки. Параметры в файле local.settings.json используются только в том случае, при локальном запуске. Файл с локальными параметрами имеет следующую структуру:

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

При локальном запуске, поддерживаются следующие параметры:

| Параметр      | Описание                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Если задано значение `true`, все значения шифруются с помощью ключа локального компьютера. Используется с командами `func settings`. По умолчанию имеет значение `false`. |
| **`Values`** | Массив параметров приложения и строки подключения, используемые при локальном запуске. Эти пары ключ значение (строка строка) соответствуют параметры приложения в приложении-функции в Azure, такие как [ `AzureWebJobsStorage` ]. Многие триггеры и привязки имеют свойство, которое ссылается на параметр приложения строки подключения, например `Connection` для [триггер хранилища BLOB-объектов](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration). Для таких свойств требуется параметр приложения, определенный в `Values` массива. <br/>[`AzureWebJobsStorage`] Обязательное приложение устанавливает для триггеров, отличных от HTTP. <br/>Версии 2.x среды выполнения функций требует [`FUNCTIONS_WORKER_RUNTIME`] параметр, который создается для проекта, основными инструментами. <br/> При наличии [эмулятора хранения Azure](../articles/storage/common/storage-use-emulator.md) установлены локально, можно задать [ `AzureWebJobsStorage` ] для `UseDevelopmentStorage=true` и основных инструментов используется эмулятор. Во время разработки это удобно, но следует проверить подключение к фактическому хранилищу перед развертыванием.<br/> Значения должны быть строки и не JSON объекты или массивы. Имена параметров не может содержать двоеточия (`:`) или двойного подчеркивания (`__`); они зарезервированы средой выполнения.  |
| **`Host`** | Параметры в этом разделе служат для настройки хост-процесса Функций при выполнении в локальной среде. Они отделены от host.json параметры, которые также применяются при работе в Azure. |
| **`LocalHttpPort`** | Задает порт по умолчанию, используемый при выполнении локального узла Функций (`func host start` и `func run`). Параметр командной строки `--port` имеет приоритет над этим значением. |
| **`CORS`** | Определяет источники, для которых разрешен [общий доступ к ресурсам независимо от источника (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Источники указываются в виде разделенного запятыми списка без пробелов. Допускается подстановочное значение (\*), разрешающее запросы из любого источника. |
| **`CORSCredentials`** |  Задайте для него значение true, чтобы разрешить `withCredentials` запросов. |
| **`ConnectionStrings`** | Не применяйте эту коллекцию для строк подключения, используемых функциями привязки. Эта коллекция используется только с платформ, которые обычно получают строк соединения из `ConnectionStrings` файл раздел конфигурации, такие как [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Строки подключения, содержащиеся в этом объекте, добавляются в среду с типом поставщика [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Элементы этой коллекции не публикуются в Azure с другими параметрами приложения. Необходимо явным образом добавить эти значения для `Connection strings` коллекцию параметрах приложения-функции. Если вы создаете [ `SqlConnection` ](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) в коде функции следует хранить значение строки подключения в **параметры приложения** на портале с другими подключениями. |

[«AzureWebJobsStorage»]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
