---
title: Устранение неполадок брандмауэра веб-приложения для шлюза приложений Azure
description: В этой статье содержатся сведения об устранении неполадок для брандмауэра веб-приложения (WAF) шлюза приложений Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 6/11/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: b81ffc5769fa5521094734da5d00aab77aa7cd35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082447"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Устранение неполадок брандмауэра веб-приложения (WAF) для шлюза приложений Azure

Существует несколько вещей, которые можно сделать, если запросы, которые должны пройти через брандмауэр веб-приложения (WAF) будут заблокированы.

Во-первых, убедитесь, вы прочитали [Общие сведения о WAF](waf-overview.md) и [конфигурации WAF](application-gateway-waf-configuration.md) документов. Кроме того, убедитесь, что вы включили [мониторинг WAF](application-gateway-diagnostics.md) в этой статье рассматривается, как функции WAF, как правило WAF задает работы и как получить доступ к журналы WAF.

## <a name="understanding-waf-logs"></a>Общие сведения о WAF журналах

Журналы WAF предназначена для отображения каждого запроса, однако и блокирует брандмауэр веб-приложения. Это книги учета всех вычисленных запросов, которые сопоставлены или заблокирован. Если вы заметили, что WAF блокирует запрос, что не следует (ложный положительный результат), можно выполнить ряд действий. Во-первых сузить список и найдите конкретному запросу. Просмотрите журналы, чтобы найти определенный идентификатор URI, метка времени или операции запроса. Найдя связанные записи, можно начать действовать ложных положительных результатов.

Предположим, у вас есть допустимого трафика, содержащий строку *1 = 1* , должны проходить через WAF. Если вы попытаетесь запрос, WAF блокирует трафик, который содержит ваши *1 = 1* строку в любой параметра или поля. Это строка, часто ассоциируемый с атакой путем внедрения кода SQL. Можно просматривать журналы и отметка времени запроса и правила, которые заблокированы соответствует см. в разделе.

В следующем примере вы увидите, что четыре правила активируются во время того же запроса (с помощью поля TransactionId). Первый из них, — говорит он соответствует, так как пользователь использовал числовые или IP-адрес URL-адрес для запроса, что повышает показатель аномалии, три, так как это предупреждение. Следующее правило, с которым является 942130, который является то, что вы ищете. Вы увидите *1 = 1* в `details.data` поля. Это повышает показатель аномалии 3 опять же, как это предупреждение. Как правило, для каждого правила, которое имеет действие **Matched** оценки аномалий увеличивается, и на этом этапе показатель аномалии будет шесть. Дополнительные сведения см. в разделе [режим оценки аномалий](waf-overview.md#anomaly-scoring-mode).

Окончательные записи двух журналов показывают, что запрос был заблокирован, так как показатель аномалий была достаточно высокое. Эти операции имеют различные действия, чем другие два. Вы узнаете, они фактически *заблокирован* запроса. Эти правила являются обязательными и не может быть отключено. Они не должны рассматриваться как правила, а также более как основную инфраструктуру внутренних механизмов WAF.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Устранение ложных положительных результатов

Эти сведения и знания, правило 942130 является тот, который соответствует *1 = 1* строки, можно выполнить ряд действий, чтобы запретить это блокирование трафика:

- Используйте список исключений

   См. в разделе [конфигурации WAF](application-gateway-waf-configuration.md#waf-exclusion-lists) Дополнительные сведения о исключения списков.
- Отключите правило.

### <a name="using-an-exclusion-list"></a>С помощью списка исключений

Чтобы принять обоснованное решение об обработке ложное положительное, важно ознакомиться с технологиями, которые использует приложение. Например, не SQL server в стеке технологий, и вы получаете ложных положительных результатов, связанные с этим правилам. Отключение этих правил не ослабить обязательно безопасности.

Одним из преимуществ использования списка исключений — отключенное только определенной части запроса. Тем не менее это означает, что определенные исключения применяется ко всему трафику, проходящему через WAF, так как это глобальный параметр. Например, это может привести к проблеме, если *1 = 1* является допустимым запросом в тексте для определенного приложения, но не для других пользователей. Еще одно преимущество — что можно выбрать между тело, заголовки и файлы cookie должны быть исключены, если выполнено определенное условие, в отличие от за исключением запрос целиком.

Иногда бывают случаи, где определенные параметры передается в брандмауэр веб-приложения таким образом, не может быть интуитивно. Например есть маркер, который передается при проверке подлинности с помощью Azure Active Directory. Этот токен *__RequestVerificationToken*, обычно передается в виде файла Cookie запроса. Однако в некоторых случаях, где файлы cookie отключены, этот токен также передаются как атрибут запроса или «arg». В этом случае необходимо убедиться, что *__RequestVerificationToken* добавляется в список исключений, как **имя атрибута запроса** также.

![Исключения](media/waf-tshoot/exclusion-list.png)

В этом примере требуется исключить **имя атрибута запроса** , равным *text1*. Это видно, так как вы увидите имя атрибута в журналах брандмауэра: **данных: Сопоставленные данные: 1 = 1 в ARGS:text1: 1=1**. Атрибут является **text1**. Можно также найти это имя атрибута несколько других способов, см. в разделе [поиск запроса имена атрибутов](#finding-request-attribute-names).

![Списки исключений WAF](media/waf-tshoot/waf-config.png)

### <a name="disabling-rules"></a>Отключение правил

Еще один способ обойти ложное положительное является, чтобы отключить правило, с которым на мысль ввода брандмауэр веб-приложения был вредоносных. Так как проанализировать журналы WAF и сведена правило вниз до 942130, его можно отключить на портале Azure. См. в разделе [Настройка правил брандмауэра веб-приложения на портале Azure](application-gateway-customize-waf-rules-portal.md).

Одним из преимуществ отключении правила является, если известно, что весь трафик, который содержит условие, обычно будет блокироваться это нормальный трафик, можно отключить это правило для всей WAF. Тем не менее если только это нормальный трафик, в случае использования, можно открыть уязвимость, отключение этого правила для всей WAF, так как это глобальный параметр.

Если вы хотите использовать Azure PowerShell, см. в разделе [Настройка правил брандмауэра веб-приложения с помощью PowerShell](application-gateway-customize-waf-rules-powershell.md). Если вы хотите использовать Azure CLI, см. в разделе [Настройка правил брандмауэра веб-приложения с помощью Azure CLI](application-gateway-customize-waf-rules-cli.md).

![Правила WAF](media/waf-tshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Имена атрибутов поиск запроса

С помощью [Fiddler](https://www.telerik.com/fiddler), проверять отдельные запросы и определить, какие конкретного поля, веб-страницы, называются. Это позволяет исключить определенные поля из проверки, используя списки исключений.

В этом примере можно видеть, что поле где *1 = 1* строка была введена называется **text1**.

![Fiddler](media/waf-tshoot/fiddler-1.png)

Это поле, которые могут быть исключены. Дополнительные сведения о списки исключений, см. в разделе [ограничения на размер запроса брандмауэра Web application и списки исключений](application-gateway-waf-configuration.md#waf-exclusion-lists). Вы можете исключить оценки таким образом, настроив следующие исключения:

![Исключения WAF](media/waf-tshoot/waf-exclusion-02.png)

Также можно просматривать журналы брандмауэра, чтобы получить сведения, чтобы увидеть, что необходимо добавить в список исключений. Чтобы включить ведение журнала, см. в разделе [работоспособности серверной части, журналы диагностики и метрики для шлюза приложений](application-gateway-diagnostics.md).

В журнале брандмауэра и просмотреть файл PT1H.json за час, созданием запроса, который требуется проверить.

В этом примере вы увидите, что у вас есть четыре правила с тем же TransactionID, и что все они произошла точное одновременно:

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

С вашего ведома как рабочих наборов правил CRS и набор правил CRS 3.0 работает с системы оценки аномалий (см. в разделе [брандмауэр веб-приложения для шлюза приложений Azure](waf-overview.md)) вы знаете, что нижней два правила с  **действие: Заблокированные** блокируют свойство на основе оценки общее аномалий. Правила, чтобы сосредоточиться на являются верхними два.

Первая запись регистрируется, так как пользователь использовал числовые IP-адрес для перехода к шлюза приложений, который в этом случае можно игнорировать.

Второй одним (правило 942130) является его. Вы увидите в сведениях о его, соответствующих шаблону (1 = 1), что поле называется **text1**. Выполните предыдущие шаги, чтобы исключить **запросить имя атрибута** , **равно** **1 = 1**.

## <a name="finding-request-header-names"></a>Имена заголовков запросов на поиск

Fiddler — это полезное средство еще раз, чтобы найти имена заголовков запросов. На следующем снимке экрана видно заголовки для данного запроса GET, которые включают *Content-Type*, *User-Agent*, и т. д.

![Fiddler](media/waf-tshoot/fiddler-2.png)

Другой способ просмотра заголовки запросов и ответов — взгляд изнутри средства разработчика Chrome. Можно нажать клавишу F12 или щелкните правой кнопкой мыши "->" **инспектировать** -> **средств разработчика**и выберите **сети** вкладки. Загрузить веб-страницу и щелкните запрос, который требуется проверить.

![F12 для Chrome](media/waf-overview/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Имена файлов cookie запроса поиск

Если запрос содержит файлы cookie, **файлы cookie** можно выбрать вкладку, чтобы просмотреть их в Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Ограничения глобальных параметров, чтобы исключить ложные срабатывания

- Отключение проверки тела запроса

   Установив **инспектировать текст запроса** задано значение off, тела запроса весь трафик не будет оцениваться по WAF. Это может быть полезно в том случае, если вы знаете, что тексты запросов не вредоносных в приложение.

   Отключив этот параметр, только текст запроса не проверяется. Заголовки и файлы cookie остаются проверявшихся, если отдельные их них исключаются с помощью функции списка исключений.

- Ограничения размера файла

   Ограничение на размер файла для вашего WAF, тем самым будут ограничены вероятность атаки в веб-серверов. Позволяя больших файлов для отправки, возрастает уровень риска перегрузку серверной части. Ограничение размера файла в случае обычного использования для приложения является просто еще один способ предотвращения атак.

   > [!NOTE]
   > Если вы знаете, что любой Отправка файла никогда не будут требоваться приложению выше заданного размера, можно ограничить, задав ограничение.

## <a name="next-steps"></a>Дальнейшие действия

См. в разделе [Настройка брандмауэра веб-приложения в шлюзе приложений](tutorial-restrict-web-traffic-powershell.md).
