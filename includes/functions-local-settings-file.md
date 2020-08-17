---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 6fd8c3c5839d4cc897caa2dff70af87980e547eb
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206729"
---
## <a name="local-settings-file"></a>Файл с локальными параметрами

В локальном файле local.settings.json хранятся параметры приложения, строки подключения и параметры для локальных средств разработки. Параметры в файле local.settings.json используются только при локальном выполнении проектов. Файл локальных параметров имеет следующую структуру:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
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

При локальном выполнении проектов поддерживаются следующие параметры:

| Параметр      | Описание                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Если этот параметр имеет значение `true`, все значения шифруются с помощью ключа локального компьютера. Используется с командами `func settings`. Значение по умолчанию — `false`. |
| **`Values`** | Массив параметров приложения и строк подключения, используемых при локальном выполнении проекта. Эти пары "ключ — значение" (строковые выражения) соответствуют параметрам приложения-функции в Azure, например [`AzureWebJobsStorage`]. Многие триггеры и привязки имеют свойство, относящееся к строке подключения параметра приложения, например `Connection` для [триггера хранилища BLOB-объектов](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Для таких свойств требуется определить параметр приложения в массиве `Values`. <br/>[`AzureWebJobsStorage`] — это обязательный параметр приложения для триггеров, отличающихся от триггеров HTTP. <br/>Для среды выполнения Функций версии 2.x и выше требуется параметр [`FUNCTIONS_WORKER_RUNTIME`], который создается для проекта автоматически в Core Tools. <br/> При наличии локального [эмулятора хранилища Azure](../articles/storage/common/storage-use-emulator.md) вы можете задать для [`AzureWebJobsStorage`] значение `UseDevelopmentStorage=true`, чтобы в Core Tools использовался эмулятор. Эмулятор удобно использовать во время разработки, но перед развертыванием следует проверить работу с реальным подключением к хранилищу.<br/> Значения должны быть строками, а не объектами JSON или массивами. Имена параметров не могут содержать двоеточие (`:`) или двойное подчеркивание (`__`). Эти символы зарезервированы средой выполнения. <br/>Чтобы отключить функцию при локальном выполнении, добавьте в коллекцию `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"`, где `<FUNCTION_NAME>` — имя функции. См. сведения о том, как [отключать и включать функции в Функциях Azure](../articles/azure-functions/disable-function.md#localsettingsjson).  |
| **`Host`** | Параметры в этом разделе служат для настройки хост-процесса Функций при выполнении проектов в локальной среде. Эти параметры отделены от параметров host.json, которые также применяются при выполнении проектов в Azure. |
| **`LocalHttpPort`** | Задает порт по умолчанию, используемый при выполнении локального узла Функций (`func host start` и `func run`). Аргумент командной строки `--port` имеет приоритет над этим параметром. |
| **`CORS`** | Определяет источники, для которых разрешен [общий доступ к ресурсам независимо от источника (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Источники указываются в виде разделенного запятыми списка без пробелов. Допускается подстановочное значение (\*), разрешающее запросы из любого источника. |
| **`CORSCredentials`** |  Если задано значение `true`, разрешает запросы `withCredentials`. |
| **`ConnectionStrings`** | Коллекция. Не применяйте эту коллекцию для строк подключения, используемых привязками функций. Эта коллекция используется только платформами, которые обычно получают строки подключений из раздела `ConnectionStrings` в файле конфигурации, как например [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Строки подключения, содержащиеся в этом объекте, добавляются в среду с типом поставщика [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Элементы этой коллекции не публикуются в Azure с другими параметрами приложения. Необходимо явным образом добавить эти значения в коллекцию `Connection strings` в настройках приложения-функции. Если вы создаете параметр [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) в коде функции, следует сохранить значение строки подключения на портале в **Параметрах приложения**, вместе с другими подключениями. |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
