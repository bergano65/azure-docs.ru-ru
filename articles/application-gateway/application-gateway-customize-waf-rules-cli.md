---
title: Настройка правил брандмауэра веб-приложения в шлюзе приложений Azure — Azure CLI
description: Эта статья содержит сведения о том, как настроить правила брандмауэра веб-приложения в шлюзе приложений с помощью Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 70377e6414b41669b6d3e991d24136a3cc4270be
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618656"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-cli"></a>Настройка правил брандмауэра веб-приложения с помощью Azure CLI

Брандмауэр веб-приложения (WAF) шлюза приложений Azure обеспечивает защиту веб-приложений с помощью основного набора правил (CRS) открытого проекта безопасности веб-приложений (OWASP). Некоторые правила могут приводить к ложным срабатываниям и блокировке реального трафика. Поэтому шлюз приложений предоставляет возможность настроить правила и группы правил. Дополнительные сведения о конкретных правилах и группах правил см. в статье [Список групп правил и правил CRS брандмауэра веб-приложения](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Просмотр правил и групп правил

Ниже приведены примеры кода для просмотра правил и групп правил, которые можно настроить.

### <a name="view-rule-groups"></a>Просмотр групп правил

В следующем примере показано, как просмотреть группы правил:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

В результатах далее представлен сокращенный ответ из предыдущего примера.

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Просмотр правил в группе правил

В примере ниже показано, как просмотреть правила в указанной группе правил.

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

В результатах далее представлен сокращенный ответ из предыдущего примера.

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Отключение правил

В следующем примере отключаются правила `910018` и `910017` в шлюзе приложений.

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="mandatory-rules"></a>Обязательные правила

Следующий список содержит условия, вызывающие WAF заблокировать запрос в режиме предотвращения (в режиме обнаружения, они регистрируются как исключения). Они не может быть задана или отключена:

* Не удалось проанализировать текст запроса приводит к запрос блокируется, если проверка тела находится в отключенном состоянии (XML, JSON, данные формы)
* Длина данных запроса текст (с файлы не) превышает установленный предел
* Текст (включая файлы) превышает ограничение на запрос
* Произошла внутренняя ошибка в модуль WAF

Конкретных 3.x CRS:

* Входящие аномалий показатель превышает порог

## <a name="next-steps"></a>Следующие шаги

После настройки с отключением правил вы можете узнать, как просматривать журналы WAF. Дополнительные сведения см. в разделе [Журналы диагностики](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
