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
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 97a3d63f94ca7e60f57177703a98b60e3f6c42ec
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849189"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Руководство по настройке политики геофильтрации брандмауэра веб-приложения (WAF) для службы Front Door
В этом руководстве показано, как с помощью Azure PowerShell создать простую политику геофильтрации и связать ее с имеющимся узлом внешнего интерфейса Front Door. Этот пример политики геофильтрации будет блокировать запросы от других стран или регионов, кроме США.

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
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Создание профиля Front Door
Создайте профиль Front Door, следуя инструкциям, описанным в статье [Краткое руководство. Создание профиля Front Door для высокодоступного глобального веб-приложения](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Определение условий соответствия геофильтрации

Создайте пример условия соответствия, по которому выбираются запросы, поступающие не из региона "США", с использованием [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) в параметрах при создании условия соответствия. [Здесь](front-door-geo-filtering.md) находится двухбуквенный код страны для сопоставления страны.

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Добавление условия соответствия геофильтрации к правилу с действием и приоритетом

Создайте объект CustomRule `nonUSBlockRule` на основе условия соответствия, действия и приоритета с помощью [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  CustomRule может содержать несколько MatchCondition.  В этом примере для действия устанавливается блокировка, а для приоритета — значение 1 (самый высокий приоритет).

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Добавление правил в политику
С помощью команды `Get-AzResourceGroup` найдите имя группы ресурсов, содержащей профиль Front Door. Затем создайте объект политики `geoPolicy`, содержащий `nonUSBlockRule`, используя команду [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) в указанной группе ресурсов, содержащей профиль Front Door. Укажите уникальное имя для политики геофильтрации. 

В приведенном ниже примере используется имя группы ресурсов *myResourceGroupFD1* при условии, что профиль Front Door создан с помощью инструкций, приведенных в статье [Краткое руководство. Создание профиля Front Door для высокодоступного глобального веб-приложения](quickstart-create-front-door.md). В следующем примере замените имя политики *geoPolicyAllowUSOnly* уникальным именем политики.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
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
- Сведения о [Брандмауэр веб-приложения Azure](waf-overview.md).
- Дополнительные сведения о [создании Front Door](quickstart-create-front-door.md).
