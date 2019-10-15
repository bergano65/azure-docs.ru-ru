---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 801613f4e3fb1e754856d716c6815895ea5da3aa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839155"
---
Вы уже создали приложение-функцию и соответствующую учетную запись хранения в Azure. Строка подключения данной учетной записи надежно хранится в параметрах приложения в Azure. В этой статье вы напишете сообщения в очередь хранилища в пределах одной учетной записи. Чтобы подключить учетную запись хранения при локальном выполнение функции, необходимо загрузить параметры приложения в файл local.settings.json. Чтобы загрузить параметры в local.settings.json, выполните команду Azure Functions Core Tools и замените `<APP_NAME>` именем приложения-функции из предыдущей статьи.

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Возможно, вам нужно будет войти в учетную запись Azure.

> [!IMPORTANT]  
> Из-за того, что его содержимое является секретом, файл local.settings.json никогда не будет опубликован, и он должен быть исключен из системы управления версиями.

Вам нужно значение `AzureWebJobsStorage`, которое является строкой подключения к учетной записи хранилища. Это подключение используется для проверки работы привязки для вывода.
