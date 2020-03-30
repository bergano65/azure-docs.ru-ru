---
title: Устранение неприятностей - Брандмауэр веб-приложений Azure
description: В этой статье содержится информация о устранении неполадок для веб-приложений Firewall (WAF) для шлюза приложений Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 33c85752903edd618044ccbab06aff7df9a791da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74046188"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Устранение проблем в веб-приложении Firewall (WAF) для шлюза приложения Azure

Есть несколько вещей, которые вы можете сделать, если запросы, которые должны пройти через веб-приложение Firewall (WAF) заблокированы.

Во-первых, убедитесь, что вы прочитали [обзор WAF](ag-overview.md) и документы [конфигурации WAF.](application-gateway-waf-configuration.md) Кроме того, убедитесь, что вы включили [мониторинг WAF](../../application-gateway/application-gateway-diagnostics.md) Эти статьи объяснить, как функции WAF, как работает правило WAF, и как получить доступ к журналам WAF.

## <a name="understanding-waf-logs"></a>Понимание журналов WAF

Цель журналов WAF состоит в том, чтобы показать каждый запрос, который соответствует или заблокирован WAF. Это грядка всех оцененных запросов, которые совпадают или блокируются. Если вы заметили, что WAF блокирует запрос, что он не должен (ложное срабатывание), вы можете сделать несколько вещей. Во-первых, сузить, и найти конкретный запрос. Просмотрите журналы, чтобы найти конкретный URI, метку времени или идентификатор транзакции запроса. Когда вы обнаружите связанные записи журнала, вы можете начать действовать на ложных срабатываний.

Например, например, у вас есть законный трафик, содержащий строку *1'1,* который вы хотите пройти через ваш WAF. Если вы попробуете запрос, WAF блокирует трафик, содержащий строку *1'1* в любом параметре или поле. Это строка, часто связанная с атакой на инъекцию S'L. Вы можете просмотреть журналы и увидеть отметку времени запроса и правила, которые заблокированы / совпадают.

В следующем примере можно увидеть, что четыре правила срабатывают во время одного и того же запроса (с помощью поля TransactionId). Первый говорит, что он соответствует, потому что пользователь использовал числовой / IP URL для запроса, что увеличивает аномалии оценка на три, так как это предупреждение. Следующее правило, которое соответствует 942130, который является тот, который вы ищете. Вы можете увидеть *1'1* в `details.data` поле. Это еще больше увеличивает аномалии оценка на три снова, так как это также предупреждение. Как правило, каждое правило, которое имеет действие **Matched** увеличивает аномалии оценка, и в этот момент аномалия оценка будет шесть. Для получения дополнительной информации, см [Аномалия забил режим](ag-overview.md#anomaly-scoring-mode).

Последние две записи журнала показывают, что запрос был заблокирован, потому что оценка аномалии была достаточно высокой. Эти записи имеют другое действие, чем два других. Они показывают, что они фактически *заблокировали* запрос. Эти правила являются обязательными и не могут быть отключены. Они не должны рассматриваться как правила, но больше как основная инфраструктура waF внутренностих.

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

## <a name="fixing-false-positives"></a>Исправление ложных срабатываний

С этой информацией, и знание, что правило 942130 является тот, который соответствует *1'1* строки, вы можете сделать несколько вещей, чтобы остановить это от блокирования трафика:

- Используйте список исключений

   Дополнительную информацию о списках исключений можно узнать о [списках](application-gateway-waf-configuration.md#waf-exclusion-lists) исключений.
- Отключите правило.

### <a name="using-an-exclusion-list"></a>Использование списка исключений

Чтобы принять обоснованное решение об обращении с ложным срабатыванием, важно ознакомиться с технологиями, которые использует ваше приложение. Например, можно сказать, что в вашем стеке технологии нет сервера S'L, и вы получаете ложные срабатывания, связанные с этими правилами. Отключение этих правил не обязательно ослабляет вашу безопасность.

Одним из преимуществ использования списка исключений является то, что отключается только определенная часть запроса. Однако это означает, что конкретное исключение применимо ко всему трафику, проходящим через ВАШ WAF, поскольку это глобальная настройка. Например, это может привести к проблеме, если *1'1* является действительным запросом в организме для определенного приложения, но не для других. Другим преимуществом является то, что вы можете выбрать между телом, заголовками и файлами cookie, которые должны быть исключены, если определенное условие выполнено, в отличие от исключения всего запроса.

Иногда бывают случаи, когда определенные параметры передаются в WAF таким образом, что это не может быть интуитивно понятным. Например, есть маркер, который передается при проверке подлинности с помощью Active Directory Azure. Этот маркер, *__RequestVerificationToken,* обычно передается в качестве cookie-запроса. Однако в некоторых случаях, когда файлы cookie отключены, этот маркер также передается как атрибут запроса или "arg". В этом случае необходимо убедиться, что *__RequestVerificationToken* добавленвв в список исключений в качестве **имени атрибута Запроса.**

![Исключения](../media/web-application-firewall-troubleshoot/exclusion-list.png)

В этом примере необходимо исключить **имя атрибута Запроса,** равное *text1.* Это очевидно, потому что вы можете увидеть имя атрибута в журналах брандмауэра: **данные: Совпадающие данные: 1'1 найдено в ARGS:text1: 1'1**. Атрибут **text1**. Вы также можете найти это имя атрибута несколько других способов, [см. Поиск имен атрибутов запроса.](#finding-request-attribute-names)

![Списки исключений WAF](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Правила отключения

Еще один способ обойти ложное срабатывание заключается в том, чтобы отключить правило, которое соответствовало входу WAF мысли был вредоносным. Так как вы разогнали журналы WAF и сунули правило до 942130, вы можете отключить его на портале Azure. [См. Настройтесь на правилах брандмауэра веб-приложений через портал Azure.](application-gateway-customize-waf-rules-portal.md)

Одним из преимуществ отключения правила является то, что если вы знаете, что весь трафик, который содержит определенное условие, которое обычно будет заблокирован, является действительным трафиком, вы можете отключить это правило для всего WAF. Однако, если это только действительный трафик в конкретном случае использования, вы открываете уязвимость, отключая это правило для всего WAF, так как это глобальная настройка.

Если вы хотите использовать Azure [Customize web application firewall rules through PowerShell](application-gateway-customize-waf-rules-powershell.md)PowerShell, см. Если вы хотите использовать Azure [Customize web application firewall rules through the Azure CLI](application-gateway-customize-waf-rules-cli.md)CLI, см.

![Правила WAF](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Поиск имен атрибутов запроса

С помощью [Fiddler](https://www.telerik.com/fiddler)вы проверяете индивидуальные запросы и определяете, какие конкретные поля веб-страницы называются. Это может помочь исключить некоторые поля из инспекции с использованием списков исключений.

В этом примере можно увидеть, что поле, в которое была введена строка *1'1,* называется **text1**.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-1.png)

Это поле, вы можете исключить. Чтобы узнать больше о списках исключений, смотрите [ограничения размера запроса веб-приложений и списки исключений.](application-gateway-waf-configuration.md#waf-exclusion-lists) В этом случае можно исключить оценку, нанастройки следующего исключения:

![Исключение WAF](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

Вы также можете изучить журналы брандмауэра, чтобы получить информацию, чтобы увидеть, что вам нужно добавить в список исключений. Для обеспечения регистрации смотрите [работоспособность Back-end, диагностические журналы и метрики для шлюза приложений.](../../application-gateway/application-gateway-diagnostics.md)

Изучите журнал брандмауэра и просмотрите файл PT1H.json за час, который запрос, который вы хотите проверить, произошел.

В этом примере можно увидеть, что у вас есть четыре правила с тем же TransactionID, и что все они произошли в одно и то же время:

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

Зная, как работает правило CRS, и что набор правил CRS 3.0 работает с системой оценки аномалий (см. [Веб-приложение Firewall для Azure Application Gateway),](ag-overview.md)вы знаете, что два нижних правила с **действием: Заблокированное** свойство блокируется на основе общего балла аномалии. Правила, на которые следует сосредоточиться, это два верхних.

Первая запись регистрируется, поскольку пользователь использовал числовый IP-адрес для навигации по шлюзу приложения, который в этом случае может быть проигнорирован.

Второе (правило 942130) является интересным. Вы можете видеть в деталях, что он соответствует шаблону (1'1), и поле называется **text1**. Выполните те же предыдущие шаги, чтобы исключить **имя атрибута запроса,** **равное** **1'1**.

## <a name="finding-request-header-names"></a>Поиск имен заголовков запросов

Fiddler является полезным инструментом еще раз, чтобы найти имена заголовков запросов. На следующем скриншоте вы можете увидеть заголовки для этого запроса GET, которые включают *Content-Type,* *User-Agent*и так далее.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-2.png)

Другой способ просмотра заголовков запросов и ответов — заглянуть внутрь инструментов разработчика Chrome. Вы можете нажать F12 или справа кнопку - > **Inspect** -> **Developer Tools,** а также выбрать вкладку **Сети.** Загрузите веб-страницу и нажмите на запрос, который вы хотите проверить.

![Хром F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Поиск имен файлов cookie

Если запрос содержит файлы cookie, для их просмотра в Fiddler может быть выбрана вкладка **Cookies.**

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Ограничить глобальные параметры для устранения ложных срабатываний

- Отбыть запрос тела инспекции

   Устанавливая **орган запроса Inspect,** органы запроса всего трафика не будут оцениваться вашим WAF. Это может быть полезно, если вы знаете, что органы запроса не являются вредоносными для вашего приложения.

   Отключая этот параметр, только тело запроса не проверяется. Заголовки и файлы cookie остаются проверенными, если отдельные из них не исключены с использованием функциональности списка исключений.

- Ограничения размера файла

   Ограничивая размер файла для ВАшего WAF, вы ограничиваете возможность атаки на ваши веб-серверы. Путем позволять большие архивы быть uploaded, риск вашего backend быть overwhelmed увеличивает. Ограничение размера файла обычным случаем использования приложения — это просто еще один способ предотвращения атак.

   > [!NOTE]
   > Если вы знаете, что ваше приложение никогда не будет нуждаться в загрузке файлов сверх заданного размера, вы можете ограничить его, установив лимит.

## <a name="firewall-metrics-waf_v1-only"></a>Метрики брандмауэра (только WAF_v1)

Для брандмауэров веб-приложений v1 следующие метрики теперь доступны на портале: 

1. Веб-приложение Firewall Заблокирован Запрос Граф Количество запросов, которые были заблокированы
2. Веб-приложение Firewall Заблокировали Правило Отсчитывают все правила, которые были согласованы **и** запрос был заблокирован
3. Веб-приложение Firewall Всего Распределения Правил Все правила, которые были сопоставлены во время оценки
     
Для включения метрик выберите вкладку **Metrics** на портале и выберите одну из трех метрик.

## <a name="next-steps"></a>Дальнейшие действия

[Узнайте, как настроить брандмауэр веб-приложений на шлюзе приложения.](tutorial-restrict-web-traffic-powershell.md)
