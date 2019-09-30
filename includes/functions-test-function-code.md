---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 44eb25be12b908c8f951cb20948068da3bfc2928
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203128"
---
## <a name="test"></a>Проверка функции в Azure

Для проверки развернутой функции используйте cURL. С помощью URL-адреса и ключа функции, скопированным на предыдущем шаге, добавьте строку запроса `&name=<yourname>` к URL-адресу.

![Использование cURL для вызова функции в Azure](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

Кроме того, вы можете вставить скопированный URL-адрес с ключом функции в адресную строку в веб-браузере. Перед выполнением запроса добавьте в URL-адрес строку запроса `&name=<yourname>`.

![Использование веб-браузера для вызова функции](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
