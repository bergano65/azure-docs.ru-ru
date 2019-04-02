---
title: Учебник. Настройка политики геофильтрации брандмауэра веб-приложения для Azure Front Door Service | Документация Майкрософт
description: В этом руководстве вы узнаете, как создать простую политику геофильтрации и связать ее с имеющимся узлом внешнего интерфейса Front Door
services: frontdoor
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: 2553dccaa57e5340bf36bbccdf7826d242716300
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472639"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Руководство по настройке политики геофильтрации брандмауэра веб-приложения (WAF) для службы Front Door
В этом руководстве показано, как с помощью Azure PowerShell создать простую политику геофильтрации и связать ее с имеющимся узлом внешнего интерфейса Front Door. Этот пример политики геофильтрации будет блокировать запросы от других стран, кроме США.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Предварительные требования
Прежде чем начать настройку политики геофильтрации, настройте среду PowerShell и создайте профиль Front Door.
### <a name="set-up-your-powershell-environment"></a>Настройка среды PowerShell
В Azure PowerShell доступен набор командлетов, которые используют модель [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) для управления ресурсами Azure. 

Вы можете установить [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) на локальном компьютере и использовать его в любом сеансе PowerShell. Следуя инструкциям на странице, войдите с учетными данными Azure и установите модуль Az PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Подключение к Azure с помощью интерактивного диалогового окна для входа
```
Connect-AzAccount
Install-Module -Name Az
```
Убедитесь, что у вас установлена текущая версия PowerShellGet. Выполните следующую команду и снова откройте PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Установка модуля Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor -AllowPrerelease
```

### <a name="create-a-front-door-profile"></a>Создание профиля Front Door
Создайте профиль Front Door, следуя инструкциям, описанным в статье [Краткое руководство. Создание профиля Front Door для высокодоступного глобального веб-приложения](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Определение условий соответствия геофильтрации

Создайте пример условия соответствия, по которому выбираются запросы, поступающие не из региона "США", с использованием [New-AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject) в параметрах при создании условия соответствия. [Здесь](front-door-geo-filtering.md) находится двухбуквенный код страны для сопоставления страны.

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Добавление условия соответствия геофильтрации к правилу с действием и приоритетом

Создайте объект CustomRule `nonUSBlockRule` на основе условия соответствия, действия и приоритета с помощью [New-AzFrontDoorCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorcustomruleobject).  CustomRule может содержать несколько MatchCondition.  В этом примере для действия устанавливается блокировка, а для приоритета — значение 1 (самый высокий приоритет).

```
$nonUSBlockRule = New-AzFrontDoorCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Добавление правил в политику
С помощью команды `Get-AzResourceGroup` найдите имя группы ресурсов, содержащей профиль Front Door. Затем создайте объект политики `geoPolicy`, содержащий `nonUSBlockRule`, используя команду [New-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy) в указанной группе ресурсов, содержащей профиль Front Door. Укажите уникальное имя для политики геофильтрации. 

В приведенном ниже примере используется имя группы ресурсов *myResourceGroupFD1* при условии, что профиль Front Door создан с помощью инструкций, приведенных в статье [Краткое руководство. Создание профиля Front Door для высокодоступного глобального веб-приложения](quickstart-create-front-door.md).

```
$geoPolicy = New-AzFrontDoorFireWallPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Связывание политики WAF с узлом внешнего интерфейса Front Door
Свяжите объект политики WAF с имеющимся узлом внешнего интерфейса Front Door и обновите свойства Front Door. 

Для этого сначала извлеките объект Front Door с помощью команды [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Затем установите для свойства внешнего интерфейса WebApplicationFirewallPolicyLink идентификатор ресурса `geoPolicy` с помощью [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Достаточно установить свойство WebApplicationFirewallPolicyLink один раз, чтобы связать политику WAF с узлом внешнего интерфейса Front Door. Последующие обновления политики будут автоматически применяться к узлу внешнего интерфейса.

## <a name="next-steps"></a>Дополнительная информация

- [Защита на прикладном уровне с помощью Front Door](front-door-application-security.md).
- Дополнительные сведения о [создании Front Door](quickstart-create-front-door.md).
