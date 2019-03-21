---
title: Настройка правил брандмауэра веб-приложения в шлюзе приложений Azure с помощью PowerShell
description: Эта статья содержит сведения о том, как настроить правила брандмауэра веб-приложения в шлюзе приложений с помощью PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.openlocfilehash: f96395a54f66b787878faeee057f02818f956ade
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317005"
---
# <a name="customize-web-application-firewall-rules-through-powershell"></a>Настройка правил брандмауэра веб-приложения с помощью PowerShell

Брандмауэр веб-приложения (WAF) шлюза приложений Azure обеспечивает защиту веб-приложений с помощью основного набора правил (CRS) открытого проекта безопасности веб-приложений (OWASP). Некоторые правила могут приводить к ложным срабатываниям и блокировке реального трафика. Поэтому шлюз приложений предоставляет возможность настроить правила и группы правил. Дополнительные сведения о конкретных правилах и группах правил см. в статье [Список групп правил и правил CRS брандмауэра веб-приложения](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Просмотр правил и групп правил

Ниже приведены примеры кода для просмотра правил и групп правил, которые можно настроить в шлюзе приложений с включенным WAF.

### <a name="view-rule-groups"></a>Просмотр групп правил

В следующем примере показано, как просмотреть группы правил:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

В результатах далее представлен сокращенный ответ из предыдущего примера.

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>Отключение правил

В следующем примере отключаются правила `911011` и `911012` в шлюзе приложений.

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Обязательные правила

Следующий список содержит условия, вызывающие WAF заблокировать запрос в режиме предотвращения (в режиме обнаружения, они регистрируются как исключения). Они не может быть задана или отключена:

* Не удалось проанализировать текст запроса приводит к запрос блокируется, если проверка тела находится в отключенном состоянии (XML, JSON, данные формы)
* Длина данных запроса текст (с файлы не) превышает установленный предел
* Текст (включая файлы) превышает ограничение на запрос
* Произошла внутренняя ошибка в модуль WAF

Конкретных 3.x CRS:

* Входящие аномалий показатель превышает порог

## <a name="next-steps"></a>Дальнейшие действия

После настройки с отключением правил вы можете узнать, как просматривать журналы WAF. Дополнительные сведения см. в разделе [Журналы диагностики](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
