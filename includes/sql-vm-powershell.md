---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165808"
---
## <a name="start-your-powershell-session"></a>Запуск сеанса PowerShell
 

Выполните командлет [**Connect-Az Account**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount). Откроется окно входа, в котором нужно ввести свои учетные данные. Используйте для входа те же учетные данные, что и для входа на портал Azure.

```powershell
Connect-AzAccount
```

Если у вас несколько подписок, используйте командлет [**Set-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext), чтобы выбрать подписку, которую будет использовать сеанс PowerShell. Чтобы узнать, какую подписку использует текущий сеанс PowerShell, выполните командлет [**Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Чтобы просмотреть все подписки, выполните командлет [**Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

