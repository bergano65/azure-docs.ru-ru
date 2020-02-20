---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: b118da6b751bc7a1e29ceef10c91dc5e9e3659c2
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474167"
---
Атрибуты привязки определяются непосредственно в файле function.json. В зависимости от типа привязки могут потребоваться дополнительные свойства. [Параметр выходных данных очереди](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) описывает поля, требуемые для привязки очереди службы хранилища Azure. Расширение позволяет легко добавлять привязки в файл function.json. 

Чтобы создать привязку, щелкните правой кнопкой мыши (CTRL+щелчок в macOS) файл `function.json` в папке HttpTrigger и выберите **Add binding...** (Добавить привязку...). Выполните инструкции, указанные на экране, чтобы определить следующие свойства для новой привязки:

| prompt | Значение | Описание |
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