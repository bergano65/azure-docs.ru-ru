---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 64a1062a8b73768a334277eafb663a7d2d5dd59a
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838962"
---
Атрибуты привязки определяются непосредственно в файле function.json. В зависимости от типа привязки могут потребоваться дополнительные свойства. [Параметр выходных данных очереди](../articles/azure-functions/functions-bindings-storage-queue.md#output---configuration) описывает поля, требуемые для привязки очереди службы хранилища Azure. Расширение позволяет легко добавлять привязки в файл function.json. 

Чтобы создать привязку, щелкните правой кнопкой мыши (CTRL+щелчок в macOS) файл `function.json` в папке HttpTrigger и выберите **Add binding...** (Добавить привязку...). Выполните инструкции, указанные на экране, чтобы определить следующие свойства для новой привязки:

| prompt | Значение | ОПИСАНИЕ |
| -------- | ----- | ----------- |
| **Select binding direction** (Выберите направление привязки) | `out` | Привязка является выходной привязкой. |
| **Select binding with direction...** (Выберите привязку с направлением...) | `Azure Queue Storage` | Привязка является привязкой очереди службы хранилища Azure. |
| **The name used to identify this binding in your code** (Имя, используемое для идентификации этой привязки в коде) | `msg` | Имя, которое используется для идентификации параметров привязки, указанных в коде. |
| **The queue to which the message will be sent** (Очередь, в которую будет отправляться сообщение) | `outqueue` | Имя очереди, в которой записывается привязка. Если *queueName* не существует, то при первом использовании этот параметр будет создан привязкой. |
| **Select setting from "local.setting.json"** (Выберите параметр из файла local.setting.json) | `AzureWebJobsStorage` | Имя параметра приложения, который содержит строку подключения к учетной записи хранения. Параметр `AzureWebJobsStorage` содержит строку подключения учетной записи хранения, созданной в приложении-функции. |

Привязка добавляется к массиву `bindings` в файле function.json, который теперь должен выглядеть следующим образом:

```json
{
   ...

  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```