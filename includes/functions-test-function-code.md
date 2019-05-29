---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: a3f75b7273164abc5318f16e9ab8d9883ff0c0aa
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132109"
---
## <a name="test"></a>Тестирование функции в Azure

Для тестирования развернутой функции используйте cURL. Добавьте к URL-адресу, который вы скопировали на предыдущем шаге, строку запроса `&name=<yourname>`, как показано в следующем примере:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

![Использование cURL для вызова функции в Azure](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

Кроме того, вы можете вставить скопированный URL-адрес в адресную строку в веб-браузере. Перед выполнением запроса добавьте в URL-адрес строку запроса `&name=<yourname>`.

![Использование веб-браузера для вызова функции](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
