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
ms.openlocfilehash: 6741c034351099f544c20749eb7c7a39e7932181
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195134"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Настройка модуля PowerShell для виртуального рабочего стола Windows

>[!IMPORTANT]
>Это содержимое применимо к обновлению за весну 2020 года с объектами Azure Resource Manager для Виртуального рабочего стола Windows.
>
> Обновление Виртуального рабочего стола Windows за весну 2020 года пока предоставляется как общедоступная предварительная версия. Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для выполнения производственных рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. 
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

Для входа в учетную запись Azure требуется код, который создается при выполнении командлета Connect. Чтобы войти, перейдите по адресу, <https://microsoft.com/devicelogin> Введите код, а затем выполните вход с помощью учетных данных администратора Azure.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

Будет создан вход в подписку, которая по умолчанию используется для учетных данных администратора.

## <a name="change-the-default-subscription"></a>Изменение подписки по умолчанию

Если вы хотите изменить подписку по умолчанию после входа, выполните следующий командлет:

```powershell
Select-AzSubscription -SubscriptionName <preferredsubscriptionname>
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
- [Create a host pool with PowerShell](create-host-pools-powershell.md) (Создание пула узлов с помощью PowerShell)
- [Настройка метода балансировки нагрузки Виртуального рабочего стола Windows](configure-host-pool-load-balancing.md)
- [Настройка типа назначения пула узлов личного рабочего стола](configure-host-pool-personal-desktop-assignment-type.md)
- И это еще не все!

Если у вас возникли проблемы, ознакомьтесь со [статьей, посвященной устранению неполадок PowerShell](troubleshoot-powershell.md) .

