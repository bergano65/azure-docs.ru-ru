---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: f7e023bcfeaa07a4ee9a80ccf4ec17120605c1ba
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133836"
---
## <a name="test"></a>Проверка функции

Используйте cURL, чтобы проверить развернутую функцию на компьютере Mac или Linux, или Bash для компьютера с Windows. Выполните следующую команду cURL, заменив заполнитель `<app_name>` именем вашего приложения-функции. Добавьте строку запроса `&name=<yourname>` к URL-адресу.

```bash
curl https://<app_name>.azurewebsites.net/api/HttpTrigger?name=<yourname>
```  

![Ответ функции в браузере](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Если у вас нет доступного cURL в командной строке, введите тот же URL-адрес в адресной строке браузера. Еще раз замените заполнитель `<app_name>` именем приложения-функции, добавьте строку запроса `&name=<yourname>` к URL-адресу и выполните запрос.

    https://<app_name>.azurewebsites.net/api/HttpTrigger?name=<yourname>
   
![Ответ функции в браузере](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
