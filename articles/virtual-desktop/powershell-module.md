---
title: Виртуальный рабочий стол модуля PowerShell для Windows — Azure
description: Как установить и настроить модуль PowerShell для виртуальных рабочих столов Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 54501e7e00ba8a28dd7cb421232b9a9587604338
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82653136"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Настройка модуля PowerShell для виртуального рабочего стола Windows

>[!IMPORTANT]
>Это содержимое относится к обновлению пружины 2020 с Azure Resource Manager объектов виртуальных рабочих столов Windows.
>
> Обновление 2020 виртуального рабочего стола Windows в настоящее время находится в общедоступной предварительной версии. Эта предварительная версия предоставляется без соглашения об уровне обслуживания, и мы не рекомендуем использовать ее для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. 
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Модуль PowerShell для виртуальных рабочих столов Windows интегрирован в модуль Azure PowerShell. В этой статье вы узнаете, как настроить модуль PowerShell, чтобы можно было выполнять командлеты для виртуальных рабочих столов Windows.

## <a name="set-up-your-powershell-environment"></a>Настройка среды PowerShell

Чтобы приступить к работе с модулем, сначала установите [последнюю версию PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core). В настоящее время Командлеты виртуальных рабочих столов Windows работают только с PowerShell Core.

Далее необходимо установить модуль Десктопвиртуализатион для использования в сеансе PowerShell.

Выполните следующий командлет PowerShell в режиме с повышенными правами, чтобы установить модуль:

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> Если этот командлет не работает, попробуйте запустить его еще раз с повышенными разрешениями.

Затем выполните следующий командлет для подключения к Azure:

```powershell
Connect-AzAccount
```

Для входа в учетную запись Azure требуется код, который создается при выполнении командлета Connect. Чтобы войти, перейдите по <https://microsoft.com/devicelogin>адресу, введите код, а затем выполните вход с помощью учетных данных администратора Azure.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

Будет создан вход в подписку, которая по умолчанию используется для учетных данных администратора.

## <a name="change-the-default-subscription"></a>Изменение подписки по умолчанию

Если вы хотите изменить подписку по умолчанию после входа, выполните следующий командлет:

```powershell
Select-AzureSubscription -SubscriptionName <preferredsubscriptionname>
```

При выборе новой подписки не нужно указывать идентификатор подписки в запускаемых командлетах. Например, следующий командлет извлекает конкретный узел сеанса, не требуя идентификатора подписки:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

Можно также изменить подписки для каждого командлета, добавив нужное имя подписки в качестве параметра. Следующий командлет тот же, что и в предыдущем примере, за исключением идентификатора подписки, добавленного в качестве параметра для изменения подписке, используемой командлетом.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>Получение расположений

Параметр Location является обязательным для всех командлетов **New-азввд** , которые создают новые объекты.

Выполните следующий командлет, чтобы получить список расположений, которые поддерживает ваша подписка:

```powershell
Get-AzLocation
```

Выходные данные **Get-азлокатион** будут выглядеть следующим образом:

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

Зная расположение учетной записи, вы можете использовать его в командлете. Например, вот командлет, создающий пул узлов в расположении "SoutheastAsia":

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы настроили модуль PowerShell, командлеты можно использовать для выполнения всех видов действий в виртуальном рабочем столе Windows. Ниже приведены некоторые из мест, которые можно использовать в вашем модуле.

- С помощью наших [руководств по виртуальному рабочему столу Windows]() можно настроить собственную среду виртуальных рабочих столов Windows.
- [Создание пула узлов с помощью PowerShell](create-host-pools-powershell.md)
- [Настройка метода балансировки нагрузки Виртуального рабочего стола Windows](configure-host-pool-load-balancing.md)
- [Настройка типа назначения пула узлов личного рабочего стола](configure-host-pool-personal-desktop-assignment-type.md)
- И это еще не все!

Если у вас возникли проблемы, ознакомьтесь со [статьей, посвященной устранению неполадок PowerShell](troubleshoot-powershell.md) .

