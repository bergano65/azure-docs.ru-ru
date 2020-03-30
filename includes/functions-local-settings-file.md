---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205678"
---
## <a name="local-settings-file"></a>Файл с локальными параметрами

Файлы local.settings.json хранят настройки приложений, строки подключения и настройки, используемые локальными инструментами разработки. Настройки в файле local.settings.json используются только при локальном запуске проектов. Файл локальных настроек имеет такую структуру:

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

Эти параметры поддерживаются при локальном запуске проектов:

| Параметр      | Описание                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Когда эта настройка `true`установлена, все значения шифруются с помощью локального ключа машины. Используется с командами `func settings`. Значение по умолчанию: `false`. |
| **`Values`** | Массив настроек приложений и строк соединения, используемых при локальном запуске проекта. Эти пары значения ключа (строка-строка) соответствуют настройкам приложения [`AzureWebJobsStorage`]в приложении в приложении Azure, например. Многие триггеры и привязки имеют свойство, которое относится к настройке приложения строки соединения, как `Connection` для [триггера хранения Blob.](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration) Для этих свойств требуется настройка `Values` приложения, определяемая в массиве. <br/>[`AzureWebJobsStorage`]является необходимой настройкой приложения для триггеров, кроме HTTP. <br/>Версия 2.x и выше времени выполнения`FUNCTIONS_WORKER_RUNTIME`функций требует настройки, которая генерируется для вашего проекта Core Tools. <br/> Если у вас есть [эмулятор хранения Azure](../articles/storage/common/storage-use-emulator.md) `UseDevelopmentStorage=true`установлен локально, и вы установите, [`AzureWebJobsStorage`] Core Tools использует эмулятор. Эмулятор полезен при разработке, но перед развертыванием следует проверить фактическое соединение хранилища.<br/> Значения должны быть строками, а не объектами или массивами JSON. Настройка имен не может`:`включать в себя`__`толстой кишки ( ) или двойной подчеркнуть ( ). Эти символы зарезервированы временем выполнения.  |
| **`Host`** | Настройки в этом разделе настраивают процесс узла функций при локальном запуске проектов. Эти настройки отделены от настроек host.json, которые также применяются при запуске проектов в Azure. |
| **`LocalHttpPort`** | Задает порт по умолчанию, используемый при выполнении локального узла Функций (`func host start` и `func run`). Опция `--port` командной строки имеет приоритет над этим параметром. |
| **`CORS`** | Определяет источники, для которых разрешен [общий доступ к ресурсам независимо от источника (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Источники указываются в виде разделенного запятыми списка без пробелов. Допускается подстановочное значение (\*), разрешающее запросы из любого источника. |
| **`CORSCredentials`** |  При наборе `true` `withCredentials` на, позволяет запросы. |
| **`ConnectionStrings`** | Коллекция. Не используйте эту коллекцию для строк соединения, используемых привязками функции. Эта коллекция используется только инфраструктурами, которые обычно `ConnectionStrings` получают строки соединения из раздела файла конфигурации, например, [Entity Framework.](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx) Строки подключения, содержащиеся в этом объекте, добавляются в среду с типом поставщика [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Элементы в этой коллекции не публикуются в Azure с другими настройками приложения. Необходимо явно добавить эти значения `Connection strings` в настройки приложения функции. Если вы создаете [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) код функции, необходимо хранить значение строки соединения с другими соединениями в **настройках приложений** на портале. |

['AzureWebJobsStorage']: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
