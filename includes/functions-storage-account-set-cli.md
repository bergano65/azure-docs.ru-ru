---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 89e2e64910e33d43c107ee88137de718d020d7d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839137"
---
Откройте файл local.settings.json и скопируйте значение `AzureWebJobsStorage`, которое является строкой подключения к учетной записи хранилища. В качестве значения переменной среды `AZURE_STORAGE_CONNECTION_STRING` установите значение строки подключения, использовав эту команду Bash:

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Когда значение строки подключения будет установлено вместо переменной среды `AZURE_STORAGE_CONNECTION_STRING`, вы можете получить доступ к учетной записи хранения без необходимости проходить проверку подлинности каждый раз.