---
title: Предельный размер запросов брандмауэра веб-приложения и списки исключений в Шлюзе приложений Azure — портал Azure
description: В этой статье приводятся сведения о настройке предельного размера запросов брандмауэра веб-приложения и списков исключений в шлюзе приложений на портале Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.workload: infrastructure-services
ms.date: 1/29/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a814fc6e9a72ba92d915821bd1e1694366844555
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277428"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Предельный размер запросов брандмауэра веб-приложения и списки исключений

Брандмауэр веб-приложения (WAF) шлюза приложений Azure обеспечивает защиту веб-приложений В этой статье описывается настройка предельного размера запросов WAF и списков исключений.

## <a name="waf-request-size-limits"></a>Предельный размер запросов WAF

![Предельный размер запросов](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Брандмауэр веб-приложения позволяет настроить предельный размер запроса с нижней и верхней границей. Доступны следующие две конфигурации предельного размера:

- Поле "Максимальный размер текста запроса" указано в КБ и определяет общий предельный размер запроса, за исключением отправок файлов. Значение поля может варьироваться от 1 КБ (минимум) до 128 КБ (максимум). Значение по умолчанию — 128 КБ.
- Поле "Предельный размер отправки файла" указывается в МБ и определяет максимальный размер отправки файла. Минимальное значение поля — 1 МБ, максимальное — 500 МБ для крупных экземпляров SKU, а для средних — не более 100 МБ. Значение по умолчанию — 100 МБ.

WAF также позволяет настроить ручку, включающую и отключающую проверку текста запроса. По умолчанию проверка включена. Если проверка текста запроса отключена, WAF не проверяет содержимое текста сообщения HTTP. В таких случаях WAF продолжит применять свои правила к заголовкам, файлам cookie и URI. Если проверка текста запроса отключена, то поле максимального размера текста запроса будет неприменимо и его невозможно будет настроить. Отключение проверки текста запроса позволяет отправлять в брандмауэр веб-приложения сообщения, длина которых превышает 128 Кбайт, но текст сообщения не проверяется на наличие уязвимостей.

## <a name="waf-exclusion-lists"></a>Списки исключений WAF

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

Списки исключений WAF позволяют пропустить некоторые атрибуты запроса при проверке WAF. Распространенный пример — вставленные токены Active Directory, которые используются для проверки подлинности или полей пароля. Такие атрибуты часто могут содержать специальные символы, которые активируют ложные срабатывания правил WAF. После добавления атрибута в список исключений WAF он не учитывается ни одним настроенным и активным правилом WAF. Списки исключений применяются глобально.

В списки исключений можно добавить следующие атрибуты:

* Заголовки запроса
* Объекты cookie запросов
* Тело запроса

   * Данные формы с разделителем
   * XML
   * JSON

Вы можете указать точное соответствие заголовка запроса, текста, файла cookie или атрибута строки запроса.  Либо при необходимости вы можете задать частичное соответствие. Исключение всегда составляет поле заголовка, но не его значение. Правила исключения являются глобальными в области и применяются ко всем страницам и всем правилам.

Поддерживаемые операторы критериев соответствия:

- **Equals**:  этот оператор используется для точного совпадения. Например, для выбора заголовка с именем **bearerToken** используйте оператор равенства со значением селектора **bearerToken**.
- **Starts with**: этот оператор находит все поля, которые начинаются с указанного значения селектора.
- **Ends with**:  этот оператор находит все поля запроса, которые заканчиваются на указанное значение селектора.
- **Contains**: этот оператор находит все поля запроса, которые содержат указанное значение селектора.
- **Равно любой**: Этот оператор соответствует все поля запроса. * будет значение селектора.

Во всех случаях при поиске не учитывается регистр, и в качестве селекторов запрещено использовать регулярные выражения.

### <a name="examples"></a>Примеры

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

В следующем фрагменте Azure PowerShell показано использование исключений.

```azurepowershell
// exclusion 1: exclude request head start with xyz
// exclusion 2: exclude request args equals a

$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig -MatchVariable "RequestHeaderNames" -SelectorMatchOperator "StartsWith" -Selector "xyz"

$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig -MatchVariable "RequestArgNames" -SelectorMatchOperator "Equals" -Selector "a"

// add exclusion lists to the firewall config

$firewallConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention -RuleSetType "OWASP" -RuleSetVersion "2.2.9" -DisabledRuleGroups $disabledRuleGroup1,$disabledRuleGroup2 -RequestBodyCheck $true -MaxRequestBodySizeInKb 80 -FileUploadLimitInMb 70 -Exclusions $exclusion1,$exclusion2
```

В следующем фрагменте JSON показано использование исключений.

```json
"webApplicationFirewallConfiguration": {
          "enabled": "[parameters('wafEnabled')]",
          "firewallMode": "[parameters('wafMode')]",
          "ruleSetType": "[parameters('wafRuleSetType')]",
          "ruleSetVersion": "[parameters('wafRuleSetVersion')]",
          "disabledRuleGroups": [],
          "exclusions": [
            {
                "matchVariable": "RequestArgNames",
                "selectorMatchOperator": "StartsWith",
                "selector": "a^bc"
            }
```

## <a name="next-steps"></a>Дальнейшие действия

После настройки параметров WAF вы можете узнать, как просматривать журналы WAF. Дополнительные сведения см. в разделе [Журналы диагностики](application-gateway-diagnostics.md#diagnostic-logging).
