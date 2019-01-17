---
title: Руководство. Настройка геофильтрации в домене для Azure Front Door Service | Документация Майкрософт
description: В этом руководстве вы узнаете, как создать простую политику геофильтрации и связать ее с имеющимся узлом внешнего интерфейса Front Door
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2018
ms.author: sharadag
ms.openlocfilehash: 68da9a0255cde6cbad5c675901c80193888bf255
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214884"
---
# <a name="how-to-set-up-a-geo-filtering-policy-for-your-front-door"></a>Настройка политики геофильтрации для службы AFD
В этом руководстве показано, как с помощью Azure PowerShell создать простую политику геофильтрации и связать ее с имеющимся узлом внешнего интерфейса Front Door. Этот пример политики геофильтрации будет блокировать запросы от других стран, кроме США.

## <a name="1-set-up-your-powershell-environment"></a>1. Настройка среды PowerShell
В Azure PowerShell доступен набор командлетов, которые используют модель [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) для управления ресурсами Azure. 

Вы можете установить [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) на локальном компьютере и использовать его в любом сеансе PowerShell. Следуйте инструкциям на странице, чтобы войти с использованием учетных данных Azure, и установите AzureRM.
```
# Connect to Azure with an interactive dialog for sign-in
Connect-AzureRmAccount
Install-Module -Name AzureRM
```
> [!NOTE]
>  Поддержка [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) ожидается в ближайшее время.

Перед установкой модуля Front Door убедитесь, что у вас установлена текущая версия PowerShellGet. Выполните следующую команду и снова откройте PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

Установите модуль AzureRM.FrontDoor. 

```
Install-Module -Name AzureRM.FrontDoor -AllowPrerelease
```

## <a name="2-define-geo-filtering-match-conditions"></a>2. Определение условий соответствия геофильтрации
Сначала создайте пример условия соответствия, которое выбирает запросы, поступающие не от "США". Ознакомьтесь с [руководством](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoormatchconditionobject) по параметрам PowerShell при создании условия соответствия. [Здесь](front-door-geo-filtering.md) находится двухбуквенный код страны для сопоставления страны.

```
$nonUSGeoMatchCondition = New-AzureRmFrontDoorMatchConditionObject -MatchVariable RemoteAddr -OperatorProperty GeoMatch -NegateCondition $true -MatchValue "US"
```
 
## <a name="3-add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>3. Добавление условия соответствия геофильтрации к правилу с действием и приоритетом

Затем создайте объект CustomRule `nonUSBlockRule` на основе условия соответствия, действия и приоритета.  CustomRule может содержать несколько MatchCondition.  В этом примере для действия устанавливается блокировка, а для приоритета — значение 1 (самый высокий приоритет).

```
$nonUSBlockRule = New-AzureRmFrontDoorCustomRuleObject -Name "geoFilterRule" -RuleType MatchRule -MatchCondition $nonUSGeoMatchCondition -Action Block -Priority 1
```

Ознакомьтесь с [руководством](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorcustomruleobject) по параметрам PowerShell при создании CustomRuleObject.

## <a name="4-add-rules-to-a-policy"></a>4. Добавление правил в политику
На этом шаге создается объект политики `geoPolicy`, содержащий `nonUSBlockRule` из предыдущего шага в указанной группе ресурсов. Используйте `Get-AzureRmResourceGroup`, чтобы найти ResourceGroupName $resourceGroup.

```
$geoPolicy = New-AzureRmFrontDoorFireWallPolicy -Name "geoPolicyAllowUSOnly" -resourceGroupName $resourceGroup -Customrule $nonUSBlockRule  -Mode Prevention -EnabledState Enabled
```

Ознакомьтесь с [руководством](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorfirewallpolicy) по параметрам PowerShell при создании политики.

## <a name="5-link-policy-to-a-front-door-frontend-host"></a>5. Связывание политики с узлом внешнего интерфейса Front Door
На последних шагах потребуется связать объект политики защиты с имеющимся узлом внешнего интерфейса Front Door и свойством обновления Front Door. Сначала нужно получить объект Front Door с помощью команды [Get-AzureRmFrontDoor](https://docs.microsoft.com/azure/frontdoor/get-azurermfrontdoor), а затем установить для свойства внешнего интерфейса WebApplicationFirewallPolicyLink идентификатором ресурса `geoPolicy`.

```
$geoFrontDoorObjectExample = Get-AzureRmFrontDoor -ResourceGroupName $resourceGroup
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Чтобы обновить объект Front Door, используйте следующую [команду](https://docs.microsoft.com/azure/frontdoor/set-azurermfrontdoor).

```
Set-AzureRmFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Достаточно установить свойство WebApplicationFirewallPolicyLink один раз, чтобы связать политику защиты с узлом внешнего интерфейса Front Door. Последующие обновления политики будут автоматически применяться к узлу внешнего интерфейса.

## <a name="next-steps"></a>Дополнительная информация

- [Защита на прикладном уровне с помощью Front Door](front-door-application-security.md).
- Дополнительные сведения о [создании Front Door](quickstart-create-front-door.md).
