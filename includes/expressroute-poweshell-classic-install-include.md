---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926213"
---
Установите последние версии модулей PowerShell управления службами Azure (SM) и модуля ExpressRoute. Среду Azure CloudShell нельзя использовать для запуска модулей SM.

1. Используйте инструкции из статьи [Установка модуля управления службами](/powershell/azure/servicemanagement/install-azure-ps) для установки модуля управления службами Azure. Если модуль az или RM уже установлен, обязательно используйте параметр-AllowClobber.
2. Импортируйте установленные модули. При использовании следующего примера измените путь в соответствии с расположением и версией установленных модулей PowerShell.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Чтобы войти в учетную запись Azure, откройте консоль PowerShell с повышенными правами и подключитесь к вашей учетной записи. Используйте следующий пример, чтобы подключиться с помощью модуля управления службами:

   ```powershell
   Add-AzureAccount
   ```