---
title: Обновление схемы анализа трафика Azure - март 2020 г. Документы Майкрософт
description: Примеры запросов с новыми полями в схеме анализа трафика.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/06/2020
ms.author: vinigam
ms.openlocfilehash: 0e9d37e3a89473e59b94168f8f8c80e7a6621107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969060"
---
# <a name="sample-queries-with-new-fields-in-traffic-analytics-schema-august-2019-schema-update"></a>Примеры запросов с новыми полями в схеме Traffic Analytics (обновление схемы схемы августа 2019 г.)

Схема [журнала «Лог аналитики трафика»](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema) была обновлена, чтобы включить следующие новые поля: **SrcPublicIPs_s,** **DestPublicIPs_s,** **NSGRule_s.** В ближайшие месяцы будут обезвлечены следующие старые поля: **VMIP_s,** **Subscription_g,** **Region_s,** **NSGRules_s,** **Subnet_s,** **VM_s,** **NIC_s PublicIPs_s,** **FlowCount_d.** **PublicIPs_s**
Новые поля предоставляют информацию об испытом и назначения и упрощают запросы.

Ниже приведены три примера, показывающие, как заменить старые поля новыми.

## <a name="example-1---vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-publicips_s"></a>Пример 1 - VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s, PublicIPs_s

Нам не нужно делать выводобки об исходных и назначенияных случаях для Azure и внешних общедоступных потоков из FlowDirection_s поле для потоков AzurePublic и ExternalPublic. В случае NVA (сетевого виртуального прибора) FlowDirection_s поле может быть неуместным для использования.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```


## <a name="example-2---nsgrules_s"></a>Пример 2 - NSGRules_s

Ранее поле было формата: <индекс значение 0)><NSG_RULENAME><Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>

Ранее мы использовали для агрегирования данных по NSG и NSGRules. Теперь мы не агрегируем. Таким образом, NSGList_s содержит только один NSG и NSGRules_s также используется для содержат только одно правило. Таким образом, мы удалили сложный форматирование здесь и то же самое можно найти в других областях, как упоминалось ниже:

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3---flowcount_d"></a>Пример 3 - FlowCount_d

Поскольку мы не перемещаем данные по NSG, FlowCount_d просто AllowedInFlows_d и DeniedInFlows_d AllowedOutFlows_d DeniedOutFlows_d.
Только 1 из вышеперечисленных 4 будет ненулевым, а остальные три будут 0. И это будет означать статус и рассчитывать в NIC, где поток был захвачен.

Если поток был разрешен, одно из полей, закрепленных на "Разрешено", будет заселено. В противных отношениях будут заселены одно поле, закрепленное на "Отказе".
Если поток был входящим, одно из полей, суффиксированных с "d",\_как "InFlows_d" суффиксное поле будет заселено. Еще "OutFlows_d" будет заселен.

В зависимости от более чем 2 условий, мы знаем, какой из 4 будет заселен.


## <a name="next-steps"></a>Next Steps
Чтобы получить ответы на часто задаваемые вопросы, [Traffic analytics documentation](traffic-analytics.md) см. [Traffic analytics FAQ](traffic-analytics-faq.md)
