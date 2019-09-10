---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9381f436aaeedb31732f853a6c4765ac43c6a752
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70170895"
---
## <a name="test"></a>Тестирование функции в Azure

Для тестирования развернутой функции используйте cURL. С помощью URL-адреса и ключа функции, скопированным на предыдущем шаге, добавьте строку запроса `&name=<yourname>` к URL-адресу.

![Использование cURL для вызова функции в Azure](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

Кроме того, вы можете вставить скопированный URL-адрес с ключом функции в адресную строку в веб-браузере. Перед выполнением запроса добавьте в URL-адрес строку запроса `&name=<yourname>`.

![Использование веб-браузера для вызова функции](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
