---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74926213"
---
Установите последние версии модулей PowerShell управления службами Azure (SM) и модуля ExpressRoute. Для запуска Модулей SM нельзя использовать среду Azure CloudShell.

1. Для установки модуля управления службой воспользуйтесь инструкциями в статье [«Установка модуля управления обслуживанием».](/powershell/azure/servicemanagement/install-azure-ps) Если у вас уже установлен модуль Az или RM, обязательно используйте '-AllowClobber'.
2. Импортируйте установленные модули. При использовании следующего примера отрегулируйте путь, чтобы отразить местоположение и версию установленных модулей PowerShell.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Чтобы войти в учетную запись Azure, откройте консоль PowerShell с повышенными правами и подключитесь к учетной записи. Используйте следующий пример, чтобы помочь вам подключиться с помощью модуля управления обслуживанием:

   ```powershell
   Add-AzureAccount
   ```