---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9c972508c98e87771154bd26cc166c6bea4201ee
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279507"
---
## <a name="test"></a>Проверка функции в Azure

Для проверки развернутой функции можно использовать веб-браузер.  Скопируйте URL-адрес с ключом функции в адресную строку в веб-браузере. Перед выполнением запроса добавьте в URL-адрес строку запроса `&name=<yourname>`.

![Использование веб-браузера для вызова функции](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  

Кроме того, для проверки развернутой функции можно использовать cURL. С помощью URL-адреса и ключа функции, скопированным на предыдущем шаге, добавьте строку запроса `&name=<yourname>` к URL-адресу.

![Использование cURL для вызова функции в Azure](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

