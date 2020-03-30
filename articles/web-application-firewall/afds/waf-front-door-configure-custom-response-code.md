---
title: Настроили пользовательский ответ для WAF с помощью передней двери Azure
description: Узнайте, как настроить пользовательский код ответа и сообщение, когда Web Application Firewall (WAF) блокирует запрос.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 215d4058937ad5fded6bef7a36e873b52a1b5ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185347"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Настроили пользовательский ответ для брандмауэра веб-приложений Azure

По умолчанию, когда Брандмауэр Web Application Firewall Azure Web (WAF) с Azure Front Door блокирует запрос из-за совпадающих правил, он возвращает код состояния 403 с **заблокированным** сообщением запроса. В этой статье описывается, как настроить пользовательский код состояния ответа и сообщение ответа, когда запрос заблокирован WAF.

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

В Azure выделите связанные ресурсы группе ресурсов. В этом примере вы создаете группу ресурсов с помощью [New-AzResourceGroup.](/powershell/module/Az.resources/new-Azresourcegroup)

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Создание новой политики WAF с пользовательским ответом 

Ниже приведен пример создания новой политики WAF с пользовательским кодом статуса ответа, установленным на 405, и сообщением **вам заблокировано.** с помощью [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

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

Измените пользовательский код ответа или настройки тела ответа существующей политики WAF, используя [Update-AzFrontDoorFireWallPolicy.](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy)

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
- Подробнее о [брандмауэре веб-приложений с передней дверью Azure](../afds/afds-overview.md)