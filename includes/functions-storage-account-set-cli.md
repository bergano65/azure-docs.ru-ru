---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 07780c94960c581ce69e4fdd45b346c75ab8b098
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329562"
---
### <a name="set-the-storage-account-connection"></a>Установка подключения к учетной записи хранилища

Откройте файл local.settings.json и скопируйте значение `AzureWebJobsStorage`, которое является строкой подключения к учетной записи хранилища. В качестве значения переменной среды `AZURE_STORAGE_CONNECTION_STRING` установите значение строки подключения, использовав эту команду Bash:

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Когда значение строки подключения будет установлено вместо переменной среды `AZURE_STORAGE_CONNECTION_STRING`, вы можете получить доступ к учетной записи хранения без необходимости проходить проверку подлинности каждый раз.