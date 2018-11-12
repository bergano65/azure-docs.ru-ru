---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ae88a6132c79d9d9f930f6445e53b0f0452c9f2a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262613"
---
## <a name="test"></a>Проверка функции

Используйте cURL, чтобы проверить развернутую функцию на компьютере Mac или Linux, или Bash для компьютера с Windows. Выполните следующую команду cURL, заменив заполнитель `<app_name>` именем вашего приложения-функции. Добавьте строку запроса `&name=<yourname>` к URL-адресу.

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![Ответ функции в браузере](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Если у вас нет доступного cURL в командной строке, введите тот же URL-адрес в адресной строке браузера. Еще раз замените заполнитель `<app_name>` именем приложения-функции, добавьте строку запроса `&name=<yourname>` к URL-адресу и выполните запрос.

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![Ответ функции в браузере](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
