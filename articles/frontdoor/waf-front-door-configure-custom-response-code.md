---
title: Настройка ответ для брандмауэра веб-приложения на входной двери Azure
description: Узнайте, как настроить код настраиваемый ответ и сообщение, когда запрос блокирует брандмауэр веб-приложения (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: tyao;kumud
ms.openlocfilehash: d6d73055abe972cd3b6fee253b6bdb2b082ceca8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242991"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Настройка ответ для брандмауэра Azure веб-приложения

По умолчанию, когда брандмауэр Azure веб-приложения (WAF) с входной двери Azure блокирует запрос из-за соответствующих правило, она возвращает код состояния 403 с **запрос блокирован** сообщения. В этой статье описываются способы настройки код состояния настраиваемый ответ и ответное сообщение, когда запрос блокируется WAF.

## <a name="set-up-your-powershell-environment"></a>Настройка среды PowerShell
В Azure PowerShell доступен набор командлетов, которые используют модель [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) для управления ресурсами Azure. 

Вы можете установить [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) на локальном компьютере и использовать его в любом сеансе PowerShell. Следуя инструкциям на странице, войдите с учетными данными Azure и установите модуль Az PowerShell.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Подключение к Azure с помощью интерактивного диалогового окна для входа
```
Connect-AzAccount
Install-Module -Name Az
```
Убедитесь, что у вас установлена текущая версия PowerShellGet. Выполните следующую команду и снова откройте PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Установка модуля Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

В Azure выделите связанные ресурсы группе ресурсов. В этом примере создайте группу ресурсов с помощью [New AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Создать новую политику WAF с настраиваемый ответ 

Ниже приведен пример создания новой политики WAF с кодом состояния настраиваемый ответ, значение 405, а сообщение **заблокировано.** с помощью [New AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Изменить настраиваемый ответ кода или настройки тело ответа существующей политики WAF, с помощью [AzFrontDoorFireWallPolicy обновления ](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения о [двери](front-door-overview.md)