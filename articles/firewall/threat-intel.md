---
title: Фильтрация на основе угроз Azure Firewall
description: Фильтрация на основе Microsoft Threat Intelligence может быть включена в брандмауэре с целю создания оповещений и запрета трафика, поступающего с известных вредоносных IP-адресов и доменов, а также передающегося на них.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168675"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Фильтрация на основе угроз из системы Azure Firewall

Фильтрация на основе Microsoft Threat Intelligence может быть включена в брандмауэре с целю создания оповещений и запрета трафика, поступающего с известных вредоносных IP-адресов и доменов, а также передающегося на них. IP-адреса и домены также передаются из канала Microsoft Threat Intelligence. [Интеллектуальная диаграмма безопасности](https://www.microsoft.com/en-us/security/operations/intelligence) использует интеллектуальные данные microsoft по вопросам угрозы и используется несколькими службами, включая Azure Security Center.

![Интеллект угрозы брандмауэра](media/threat-intel/firewall-threat.png)

Если вы включили фильтрацию на основе интеллекта угроз, связанные с ними правила обрабатываются до того, какэто е-либо из правил NAT, сетевых правил или правил применения.

Вы можете просто войти в оповещение, когда правило срабатывает, или вы можете выбрать режим оповещения и отрицания.

По умолчанию фильтрация на основе разведки угроз включена в режиме оповещения. Вы не можете отключить эту функцию или изменить режим до тех пор, пока интерфейс портала не станет доступен в вашем регионе.

![Интерфейс портала фильтрации на основе разведки на основе угроз](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Журналы

Следующий отрывок журнала показывает срабатывающее правило:

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

- **Исходящие тесты** - Исходящие оповещения о трафике должны быть редким явлением, так как это означает, что ваша среда была скомпрометирована. Чтобы помочь тестировать исходящие оповещения работают, был создан тестовый F-DN, который запускает оповещение. Используйте **testmaliciousdomain.eastus.cloudapp.azure.com** для исходящих тестов.

- **Входящие тестирования** - Вы можете ожидать, чтобы увидеть оповещения о входящих трафика, если правила DNAT настроены на брандмауэре. Это верно, даже если только конкретные источники допускаются на правило DNAT и трафик в противном случае отказано. Azure Firewall не предупреждает обо всех известных сканерах портов; только на сканерах, которые, как известно, также участвуют в вредоносной деятельности.

## <a name="next-steps"></a>Дальнейшие действия

- Посмотреть [образцы анализа журналов брандмауэров Azure](log-analytics-samples.md)
- Узнайте, как [развернуть и настроить брандмауэр Azure](tutorial-firewall-deploy-portal.md)
- Просмотрите [отчет разведки службы безопасности Майкрософт](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)