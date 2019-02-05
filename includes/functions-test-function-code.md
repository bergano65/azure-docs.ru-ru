---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 914c006daf49e22ebec870a549bfdbc63f882647
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55148016"
---
## <a name="test"></a>Проверка функции

Используйте cURL, чтобы проверить развернутую функцию на компьютере Mac или Linux либо с помощью PowerShell в Windows. Выполните следующую команду cURL, заменив заполнитель `<app_name>` именем вашего приложения-функции. Добавьте строку запроса `&name=<yourname>` к URL-адресу.

```powershell
Invoke-WebRequest -Uri "https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>"
```

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![Ответ функции в браузере](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Если в командной строке отсуствует `cURL` или `Invoke-WebRequest`, введите тот же URL-адрес в адресной строке браузера. Еще раз замените заполнитель `<app_name>` именем приложения-функции, добавьте строку запроса `&name=<yourname>` к URL-адресу и выполните запрос.

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![Ответ функции в браузере](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
