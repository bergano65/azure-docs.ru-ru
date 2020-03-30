---
title: Налажить правило ограничения скорости WAF для передней двери - Azure PowerShell
description: Узнайте, как настроить правило ограничения скорости для существующей конечной точки Front Door.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: b034159c3d12927f6425b3dc3c5b5609af9b0b76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649370"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Налажить правило ограничения скорости веб-приложений с помощью Azure PowerShell
Правило ограничения тарифа Azure Web Application Firewall (WAF) для Azure Front Door контролирует количество запросов, разрешенных от клиентов в течение одной минуты.
В этой статье показано, как настроить правило ограничения скорости WAF, которое контролирует количество запросов, разрешенных от клиентов к веб-приложению, *содержащему/промо* в URL с помощью Azure PowerShell.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к настройке политики ограничения скорости, навязайте среду PowerShell и создайте профиль Front Door.
### <a name="set-up-your-powershell-environment"></a>Настройка среды PowerShell
В Azure PowerShell доступен набор командлетов, которые используют модель [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) для управления ресурсами Azure. 

Вы можете установить [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) на локальном компьютере и использовать его в любом сеансе PowerShell. Следуйте инструкциям на странице, чтобы войти в систему с вашими учетными данными Azure и установить модуль Az PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Подключение к Azure с интерактивным диалогом для входа в систему
```
Connect-AzAccount

```
Перед установкой модуля Front Door убедитесь, что текущая версия PowerShellGet установлена. Выполнить следующую команду и вновь открыть PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Установка модуля Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Создание профиля Front Door
Создайте профиль передних дверей, следуя инструкциям, описанным в [профиле «Быстрый старт: Создайте переднюю дверь»](../../frontdoor/quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>Определение условий соответствия url
Определите состояние совпадения URL (URL содержит /промо) с помощью [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
Следующий пример совпадает */промо* как значение переменной *RequestUri:*

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Создание пользовательского правила ограничения скорости
Установите лимит скорости с помощью [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). В следующем примере лимит установлен до 1000. Запросы любого клиента на промо-страницу, превышающую 1000 в течение одной минуты, блокируются до следующей минуты.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Настройка политики безопасности

С помощью команды `Get-AzureRmResourceGroup` найдите имя группы ресурсов, содержащей профиль Front Door. Затем направьте политику безопасности с пользовательским правилом ограничения скорости с помощью [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) в указанной группе ресурсов, содержащей профиль Front Door.

Ниже приводится пример, в каком названии Группы ресурсов *myResourceGroupFD1* используется предположение, что вы создали профиль Front Door с помощью инструкций, предусмотренных в статье [«Быстрый старт: Создайте переднюю дверь».](../../frontdoor/quickstart-create-front-door.md)

 с помощью [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Ссылка политики на передний дом двери хост
Связать объект политики безопасности с существующим передним хостом Front Door и обновить свойства Front Door. Сначала извлеките объект Front Door с помощью команды [Get-AzFrontDoor.](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)
Затем установите переднее свойство *WebApplicationFirewallPolicyLink* на *ресурс"$ratePolicy,* созданный на предыдущем этапе с помощью команды [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 

Ниже приводится пример, в каком названии Группы ресурсов *myResourceGroupFD1* используется предположение, что вы создали профиль Front Door с помощью инструкций, предусмотренных в статье [«Быстрый старт: Создайте переднюю дверь».](../../frontdoor/quickstart-create-front-door.md) Кроме того, в приведенном ниже примере замените $frontDoorName с именем профиля Front Door. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Вам нужно только установить *свойство WebApplicationFirewallPolicyLink* один раз для того чтобы соединить политику обеспеченностью к front door front-end. Последующие обновления политики автоматически применяются к переднему концу.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [передней двери](../../frontdoor/front-door-overview.md). 


