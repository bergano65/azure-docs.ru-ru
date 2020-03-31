---
title: Настройка пользовательских правил WAF & правила по умолчанию для передней двери Azure
description: Узнайте, как настроить политику WAF, состоящую как из пользовательских, так и управляемых правил для существующей конечной точки Front Door.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 493ed1a31a23366a90b80d3ab510218c8dce0e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186634"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Налажить политику веб-приложений с помощью Azure PowerShell

Политика Azure Web Application Firewall (WAF) определяет проверки, необходимые при поступления запроса в Front Door.
В этой статье показано, как настроить политику WAF, которая состоит из некоторых пользовательских правил и с включенным набором правил по умолчанию, управляемым Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем приступить к настройке политики ограничения скорости, навязайте среду PowerShell и создайте профиль Front Door.

### <a name="set-up-your-powershell-environment"></a>Настройка среды PowerShell

В Azure PowerShell доступен набор командлетов, которые используют модель [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) для управления ресурсами Azure. 

Вы можете установить [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) на локальном компьютере и использовать его в любом сеансе PowerShell. Следуйте инструкциям на странице, чтобы войти в систему с вашими учетными данными Azure и установить модуль Az PowerShell.

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

Создайте профиль передних дверей, следуя инструкциям, описанным в [профиле «Быстрый старт: Создайте переднюю дверь»](../../frontdoor/quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Пользовательское правило, основанное на параметрах http

Ниже приводится, как настроить пользовательское правило с двумя условиями соответствия с помощью [New-AzFrontDoorWafMatchConditionObject.](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) Запросы от определенного сайта, как это определено реферером, и строка запроса не содержит "пароль". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Пользовательское правило, основанное на методе запроса http

Создайте правило, блокирующее метод «PUT», используя [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) следующим образом:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Создание пользовательского правила, основанного на ограничении размера

Следующий пример создает запросы блокировки правил с помощью Url, который длиннее 100 символов с помощью Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Добавление управляемого набора правил по умолчанию

Следующий пример создает управляемый набор правил по умолчанию с помощью Azure PowerShell:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Настройка политики безопасности

С помощью команды `Get-AzResourceGroup` найдите имя группы ресурсов, содержащей профиль Front Door. Затем назначаем политику безопасности с созданными правилами в предыдущих шагах с помощью [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) в указанной группе ресурсов, содержащей профиль Front Door.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Ссылка политики на передний дом двери хост

Связать объект политики безопасности с существующим передним хостом Front Door и обновить свойства Front Door. Во-первых, получить переднюю дверь объекта с помощью [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Далее установите передний конец *WebApplicationFirewallPolicyLink* собственности *на ресурс "$myWAFPolicy* $", созданный в предыдущем шаге с помощью [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

Ниже приводится пример, в каком названии Группы ресурсов *myResourceGroupFD1* используется предположение, что вы создали профиль Front Door с помощью инструкций, предусмотренных в статье [«Быстрый старт: Создайте переднюю дверь».](../../frontdoor/quickstart-create-front-door.md) Кроме того, в приведенном ниже примере замените $frontDoorName с именем профиля Front Door. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Вам нужно только установить *свойство WebApplicationFirewallPolicyLink* один раз для того чтобы соединить политику обеспеченностью к front door front-end. Последующие обновления политики автоматически применяются к переднему концу.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [передней двери](../../frontdoor/front-door-overview.md) 
- Узнайте больше о [WAF с передней дверью](afds-overview.md)
