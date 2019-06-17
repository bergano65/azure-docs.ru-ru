---
title: Создание и использование настраиваемых правил брандмауэра веб-приложения Azure (WAF)
description: В этой статье сведения о том, как создать настраиваемые правила брандмауэра веб-приложения (WAF) шлюза приложений Azure.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: bb8c50664261814c7d994c9b879972b1e8b846e4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66689036"
---
# <a name="create-and-use-web-application-firewall-custom-rules"></a>Создание и использование настраиваемых правил брандмауэра веб-приложения

Брандмауэр веб-приложения (WAF) шлюза приложений Azure обеспечивает защиту веб-приложений Такая защита предоставляется с Open Web Application Security Project (OWASP) Core правила задайте (CRS). В некоторых случаях может потребоваться создать собственные правила в соответствии с вашими потребностями. Дополнительные сведения о настраиваемых правил WAF см. в разделе [Обзор правил брандмауэра сервера пользовательский веб-приложения](custom-waf-rules-overview.md).

В этой статье показаны некоторые примеры пользовательских правил, которые можно создать и использовать с WAF. Дополнительные сведения о развертывании WAF с помощью настраиваемого правила с помощью Azure PowerShell, см. в разделе [настраиваемые правила настроить брандмауэр веб-приложения с помощью Azure PowerShell](configure-waf-custom-rules.md).

>[!NOTE]
> Если шлюз приложений не использует уровень WAF, на правой панели будет предоставлена возможность обновить шлюз приложений до уровня WAF.

![Включение WAF][fig1]

## <a name="example-1"></a>Пример 1

Вы знаете, есть программы-робота, с именем *evilbot* требуется блокировать при обходе веб-сайта. В этом случае будет блокировать на агенте пользователя *evilbot* в заголовках запроса.

Логика: p

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

А вот соответствующий код JSON:

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

WAF, развернутых с помощью этого правила см. в разделе [настроить пользовательское правило брандмауэра веб-приложения с помощью Azure PowerShell](configure-waf-custom-rules.md).

### <a name="example-1a"></a>Пример 1a

Можно выполнить то же самое, используя регулярное выражение.

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable `
   -Operator Regex `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name blockEvilBot `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

И соответствующий код JSON:

```json
  {
    "customRules": [
      {
        "name": "blockEvilBot",
        "ruleType": "MatchRule",
        "priority": 2,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "User-Agent",
            "matchValues": [
              "evilbot"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-2"></a>Пример 2

Вы хотите заблокировать все запросы от IP-адресов в диапазоне 198.168.5.4/24.

В этом примере будет блокировать весь трафик, поступающий из диапазона IP-адресов. Имя правила — *Правило1* и приоритет имеет значение 100.

Логика: p

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.4/24" `
   -NegationCondition $False

$rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

Ниже приведен соответствующий код JSON.

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "Block",
        "matchConditions": [
          {
            "matchVariable": "RemoteAddr",
            "operator": "IPMatch",
            "matchValues": [
              "192.168.5.4/24"
            ]
          }
        ]
      }
    ]
  }
```

Соответствующее правило CRS: `SecRule REMOTE_ADDR "@ipMatch 192.168.5.4/24" "id:7001,deny"`

## <a name="example-3"></a>Пример 3

Например, вы хотите заблокировать User-Agent *evilbot*и трафик в 192.168.5.4/24 диапазона. Для этого создайте два условия соответствия отдельных и поместить их в одном правиле. Это гарантирует, что оба *evilbot* в заголовке User-Agent **и** IP-адреса из диапазона 192.168.5.4/24 блокируются.

Логика: p **и** q

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

 $variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.4/24" `
   -NegationCondition $False

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "evilbot" `
   -Transform Lowercase `
   -NegationCondition $False

 $rule = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1, $condition2 `
   -Action Block
```

Ниже приведен соответствующий код JSON.

```json
{ 

    "customRules": [ 
      { 
        "name": "myrule", 
        "ruleType": "MatchRule", 
        "priority": 100, 
        "action": "block", 
        "matchConditions": [ 
            { 
              "matchVariable": "RemoteAddr", 
              "operator": "IPMatch", 
              "negateCondition": true, 
              "matchValues": [ 
                "192.168.5.4/24" 
              ] 
            }, 
            { 
              "matchVariable": "RequestHeaders", 
              "selector": "User-Agent", 
              "operator": "Contains", 
              "transforms": [ 
                "Lowercase" 
              ], 
              "matchValues": [ 
                "evilbot" 
              ] 
            } 
        ] 
      } 
    ] 
  } 
```

## <a name="example-4"></a>Пример 4

В этом примере нужно заблокировать, если запрос находится вне диапазона IP-адресов *192.168.5.4/24*, или строку агента пользователя не *chrome* (то есть пользователь не использует браузер Chrome). Так как используется эта логика **или**, находятся в отдельных правил для этих двух условий, как показано в следующем примере. *Правило1* и *Правило2* должны соответствовать блокировать трафик.

Логика: **не** (p **и** q) = **не** p **или не** q.

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RemoteAddr

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestHeaders `
   -Selector User-Agent

$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator IPMatch `
   -MatchValue "192.168.5.4/24" `
   -NegationCondition $True

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "chrome" `
   -Transform Lowercase `
   -NegationCondition $True

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 200 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block
```

И соответствующий код JSON:

```json
{
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "operator": "IPMatch",
            "negateCondition": true,
            "matchValues": [
              "192.168.5.4/24"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 200,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestHeaders",
            "selector": "User-Agent",
            "operator": "Contains",
            "negateCondition": true,
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "chrome"
            ]
          }
        ]
      }
    ]
  }
```

## <a name="example-5"></a>Пример 5

Вы хотите заблокировать пользовательских Обнаружение. Так как логика, используемая вот **или**, и все значения, в *RequestUri*, все из *MatchValues* может находиться в список с разделителями запятыми.

Логика: p **или** q **или** r

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri 
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1", "drop tables", "'—" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule4 `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
   -Action Block
```

Соответствующие JSON:

```json
  {
    "customRules": [
      {
        "name": "myrule4",
        "ruleType": "MatchRule",
        “priority”: 100
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1",
              "drop tables",
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

Альтернативные Azure PowerShell:

```azurepowershell
$variable1 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri
$condition1 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable1 `
   -Operator Contains `
   -MatchValue "1=1" `
   -NegationCondition $False

$rule1 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule1 `
   -Priority 100 `
   -RuleType MatchRule `
   -MatchCondition $condition1 `
-Action Block

$variable2 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition2 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable2 `
   -Operator Contains `
   -MatchValue "drop tables" `
   -NegationCondition $False

$rule2 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule2 `
   -Priority 200 `
   -RuleType MatchRule `
   -MatchCondition $condition2 `
   -Action Block

$variable3 = New-AzApplicationGatewayFirewallMatchVariable `
   -VariableName RequestUri

$condition3 = New-AzApplicationGatewayFirewallCondition `
   -MatchVariable $variable3 `
   -Operator Contains `
   -MatchValue "’—" `
   -NegationCondition $False

$rule3 = New-AzApplicationGatewayFirewallCustomRule `
   -Name myrule3 `
   -Priority 300 `
   -RuleType MatchRule `
   -MatchCondition $condition3 `
   -Action Block
```

Соответствующие JSON:

```json
  {
    "customRules": [
      {
        "name": "myrule1",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "1=1"
            ]
          }
        ]
      },
      {
        "name": "myrule2",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "transforms": [
              "Lowercase"
            ],
            "matchValues": [
              "drop tables"
            ]
          }
        ]
      },
      {
        "name": "myrule3",
        "ruleType": "MatchRule",
        "priority": 100,
        "action": "block",
        "matchConditions": [
          {
            "matchVariable": "RequestUri",
            "operator": "Contains",
            "matchValues": [
              "'--"
            ]
          }
        ]
      }
    ]
  }
```

Соответствующее правило для ModSecurity:

`SecRule REQUEST_URI "@contains 1-1" "id:7001,deny"`

`SecRule REQUEST_URI "@contains --" "id:7001,deny"`

`SecRule REQUEST_URI "@contains drop tables" "id:7001,deny"`

## <a name="next-steps"></a>Дальнейшие действия

После создания настраиваемых правил, вы узнаете, как просматривать журналы WAF. Дополнительные сведения см. в разделе [Журналы диагностики](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
