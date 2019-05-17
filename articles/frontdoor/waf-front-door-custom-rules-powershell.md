---
title: Настройка политики брандмауэра (WAF) web application с настраиваемыми правилами и набор по умолчанию Ruse для входной двери — Azure PowerShell
description: Сведения о настройке WAF политика состоит из как пользовательские, так и управляемого правила для существующей конечной точки двери.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: kumud;tyao
ms.openlocfilehash: 414869833b894e2688505a91fed8fafe0c912b73
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523743"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Настройка политики брандмауэра приложения web, с помощью Azure PowerShell
Политика брандмауэра (WAF) приложение веб-Azure определяет проверки, необходимые при получении запроса на входной двери.
В этой статье показано, как настроить политику WAF, которая состоит из некоторых настраиваемых правил и с управлением Azure набор по умолчанию Ruse включена.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия
Прежде чем начать настроить политику ограничения скорости, настройте среду PowerShell и создание передовой профиля.
### <a name="set-up-your-powershell-environment"></a>Настройка среды PowerShell
В Azure PowerShell доступен набор командлетов, которые используют модель [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) для управления ресурсами Azure. 

Вы можете установить [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) на локальном компьютере и использовать его в любом сеансе PowerShell. Следуйте инструкциям на странице, чтобы войти с использованием учетных данных Azure и установите модуль Az PowerShell.

#### <a name="sign-in-to-azure"></a>Вход в Azure
```
Connect-AzAccount

```
Перед установкой модуля Front Door убедитесь, что у вас установлена текущая версия PowerShellGet. Выполните следующую команду и снова откройте PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Установка модуля Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Создание профиля Front Door
Создайте профиль Front Door, следуя инструкциям, описанным в статье [Краткое руководство. Создание профиля передовой линии](quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Настраиваемое правило, на основе параметров http

В следующем примере показано, как настроить пользовательское правило с помощью два условия соответствия [New AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). Запросов, поступивших с указанного сайта, в соответствии с определением источника ссылки, и строка запроса не содержит «password». 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Настраиваемое правило, на основе метода запроса http
Создайте правило, блокирующее «PUT» с помощью метода [New AzFrontDoorCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) следующим образом:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Создайте настраиваемое правило на основе ограничения размера

В следующем примере создается правило блокирует запросы с URL-адрес, длина которого превышает 100 символов, с помощью Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Добавить управляемый набор по умолчанию правило

В следующем примере создается управляемый по умолчанию набор правил с помощью Azure PowerShell:
```powershell-interactive
$managedRules = New-AzFrontDoorManagedRuleObject -Type DefaultRuleSet -Version "preview-0.1"
```
## <a name="configure-a-security-policy"></a>Настройка политики безопасности

С помощью команды `Get-AzResourceGroup` найдите имя группы ресурсов, содержащей профиль Front Door. Настройте политику безопасности с правилами, созданный ранее с помощью [New AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) в указанной группе ресурсов, содержащий профиль двери.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Политики ссылки на входной двери переднего плана узел
Свяжите объект политики безопасности на существующий узел двери переднего плана и обновляет свойства двери. Сначала получите объект двери с помощью [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Затем задайте интерфейсной части *WebApplicationFirewallPolicyLink* свойства *resourceId* «$ $myWAFPolicy» создан в предыдущем шаге, с помощью [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

В приведенном ниже примере используется имя группы ресурсов *myResourceGroupFD1* при условии, что профиль Front Door создан с помощью инструкций, приведенных в статье [Краткое руководство. Создание профиля Front Door для высокодоступного глобального веб-приложения](quickstart-create-front-door.md). Кроме того, в следующем примере замените $frontDoorName на имя профиля двери. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Необходимо задать *WebApplicationFirewallPolicyLink* свойство один раз, чтобы связать политику безопасности двери переднего плана. Последующие политики обновления автоматически применяются к внешней сети.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [двери](front-door-overview.md) 
- Дополнительные сведения о [WAF для входной двери](waf-overview.md)
