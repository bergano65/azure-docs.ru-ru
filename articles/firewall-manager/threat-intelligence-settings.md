---
title: Настройка аналитики угроз в брандмауэре Azure
description: Фильтрация на основе Microsoft Threat Intelligence может быть включена в брандмауэре с целю создания оповещений и запрета трафика, поступающего с известных вредоносных IP-адресов и доменов, а также передающегося на них.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 136ceeb271bec29bdbfc4572626936ee67f05556
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568129"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Настройка аналитики угроз в брандмауэре Azure

Фильтрацию на основе аналитики угроз можно настроить для политики брандмауэра Azure, чтобы получать оповещения и отклонять трафик от известных вредоносных IP-адресов и доменов. IP-адреса и домены также передаются из канала Microsoft Threat Intelligence. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) поддерживает Microsoft Threat Intelligence и используется во множестве служб, включая Центр безопасности Azure.<br>

Если вы настроили фильтрацию на основе аналитики угроз, связанные правила обрабатываются перед любыми правилами NAT, сетевыми правилами или правилами приложений.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Политика аналитики угроз":::

## <a name="threat-intelligence-mode"></a>Режим анализа угроз

Можно выбрать регистрацию только оповещений при запуске правила или выбрать режим предупреждения и запрета.

По умолчанию фильтрация на основе аналитики угроз работает в режиме оповещения.

## <a name="allowed-list-addresses"></a>Разрешенные адреса списка

Можно настроить список разрешенных IP-адресов таким образом, чтобы аналитика угроз не отфильтровывает указанные адреса, диапазоны или подсети.



## <a name="logs"></a>Журналы

В следующем фрагменте журнала показано активированное правило.

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Тестирование

- **Тестирование исходящего трафика**. Оповещения об исходящем трафике должны появляться редко, так как они означают, что ваша среда была скомпрометирована. Чтобы проверить работоспособность оповещений об исходящем трафике, было создано тестовое полное доменное имя, которое активирует оповещение. Используйте **testmaliciousdomain.eastus.cloudapp.azure.com** для тестирования исходящего трафика.

- **Тестирование входящего трафика**. Вы можете видеть оповещения о входящем трафике, если в брандмауэре настроены правила DNAT. Это происходит, даже если в правиле DNAT разрешены только определенные источники, а трафик из других источников отклоняется. Брандмауэр Azure не оповещает о всех известных сканерах портов. Он оповещает только об известных сканерах, которые замечены во вредоносных действиях.

## <a name="next-steps"></a>Дальнейшие действия

- Изучите [аналитический отчет Microsoft Security](https://www.microsoft.com/en-us/security/operations/security-intelligence-report).