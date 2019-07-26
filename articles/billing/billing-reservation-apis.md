---
title: API-интерфейсы для автоматизации резервирования Azure | Документация Майкрософт
description: Дополнительные сведения об API-интерфейсах Azure можно использовать для программного получения сведений о резервировании.
author: yashesvi
manager: yashesvi
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/25/2019
ms.author: banders
ms.openlocfilehash: 20ed2bcf793ab5c3913ccf66d338e71c1a99a003
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478919"
---
# <a name="apis-for-azure-reservation-automation"></a>API-интерфейсы для автоматизации резервирования Azure

С помощью API-интерфейсов Azure вы можете программным способом получить сведения о резервированиях службы или программного обеспечения Azure для своей организации.

## <a name="find-reservation-plans-to-buy"></a>Поиск планов резервирования для приобретения

Используйте API рекомендаций по резервированию, чтобы получить рекомендации по приобретению планов резервирования на основе сведений об использовании ресурсов вашей организации. Дополнительные сведения см. в статье [Reserved instance purchase recommendation APIs for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation). (API-интерфейсы рекомендаций по приобретению зарезервированного экземпляра для корпоративных клиентов).

Вы также можете анализировать использование ресурсов, просмотрев сведения об использовании API потребления. Дополнительные сведения см. в разделе [Usage Details — List For Billing Period By Billing Account](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod) (Сведения об использовании. Вывод списка периода выставления счетов по учетной записи выставления счетов). Ресурсы Azure, которые вы согласованно используете, обычно являются лучшим кандидатом для резервирования.

## <a name="buy-a-reservation"></a>Покупка резервирования

Вы можете приобрести резервирование Azure и планы программного обеспечения программным путем с помощью API-интерфейсов RESTFUL. Дополнительные сведения см. в разделе [заказ на резервирование — API покупки](/rest/api/reserved-vm-instances/reservationorder/purchase).

Ниже приведен пример запроса на покупку с помощью REST API.

```
PUT https://management.azure.com/providers/Microsoft.Capacity/reservationOrders/<GUID>?api-version=2019-04-01
```

Тело запроса:

```
{
 "sku": {
    "name": "standard_D1"
  },
 "location": "westus",
 "properties": {
    "reservedResourceType": "VirtualMachines",
    "billingScopeId": "/subscriptions/ed3a1871-612d-abcd-a849-c2542a68be83",
    "term": "P1Y",
    "quantity": "1",
    "displayName": "TestReservationOrder",
    "appliedScopes": null,
    "appliedScopeType": "Shared",
    "reservedResourceProperties": {
      "instanceFlexibility": "On"
    }
  }
}
```

Вы также можете приобрести резервирование в портал Azure. Дополнительные сведения см. в следующих статьях:

Планы обслуживания:
- [Виртуальная машина](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [База данных Cosmos](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [База данных SQL](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

Планы программного обеспечения:
- [Программное обеспечение SUSE Linux](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Получение резервирования

Если вы являетесь клиентом Azure с соглашением Enterprise Agreement (клиент EA), вы можете получить резервирования, приобретенные вашей организацией, с помощью [API получения сведений о расходах по транзакциям зарезервированных экземпляров](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Для других подписок получите список приобретенных резервирований, на просмотр которых у вас есть разрешения, с помощью API [заказа на резервирование (список)](/rest/api/reserved-vm-instances/reservationorder/list). По умолчанию владелец учетной записи или тот, кто приобрел резервирование, имеет разрешения на просмотр резервирования.

## <a name="see-reservation-usage"></a>Просмотр сведений об использовании резервирования

Если вы являетесь клиентом EA, можно программным способом просмотреть сведения об использовании резервирования в вашей организации. Дополнительные сведения см. в статье [Get Reserved Instance usage for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) (Получение сведений об использовании зарезервированных экземпляров для корпоративных клиентов). Для других подписок используйте API [сводок резервирования (списки по заказам на резервирование и резервированиям)](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Если обнаружится, что резервирования в вашей организации используются неэффективно:

- Убедитесь, что виртуальные машины, создаваемые в вашей организации, соответствует размеру виртуальной машины, указанному для резервирования.
- Убедитесь, что включена гибкость размера экземпляра. Дополнительные сведения см. в разделе [Изменение области для резервирования](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Измените область резервирования на общую, чтобы она применялась шире. Дополнительные сведения см. в разделе [Изменение области для резервирования](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).
- Обменяйте неиспользуемое количество. Дополнительные сведения см. в разделе [Отмена и обмен](billing-manage-reserved-vm-instance.md#cancellations-and-exchanges).

## <a name="give-access-to-reservations"></a>Предоставление доступа к резервированиям

Получите список всех резервирований, доступных пользователю, с помощью [API списка операций резервирования](/rest/api/reserved-vm-instances/reservationorder/list). Чтобы предоставить доступ к резервированию программно, просмотрите следующие статьи:

- [Управление доступом с помощью RBAC и REST API](../role-based-access-control/role-assignments-rest.md)
- [Управление доступом с помощью RBAC и Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Управление доступом с помощью RBAC и Azure CLI](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Разделение или объединение резервирования

После приобретения нескольких экземпляров ресурсов в резервировании может потребоваться назначить экземпляры в резервировании разным подпискам. Область резервирования можно изменить, чтобы оно применялось ко всем подпискам в одном контексте выставления счетов. Однако для управления стоимостью или бюджетом можно сохранить область в рамках одной подписки и назначить экземпляры резервирования для конкретной подписки.

Чтобы разделить резервирование, используйте API [разделения резервирования](/rest/api/reserved-vm-instances/reservation/split). Разделить резервирования также можно с помощью PowerShell. Дополнительные сведения см. в разделе [Разделение отдельного резервирования на два резервирования](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Чтобы объединить два резервирования в одно, используйте API [объединения резервирований](/rest/api/reserved-vm-instances/reservation/merge).

## <a name="change-scope-for-a-reservation"></a>Изменение области резервирования

Областью резервирования может быть одна подписка, одна группа ресурсов или все подписки в контексте выставления счетов. Если задать для области значение одна подписка или одна группа ресурсов, то резервирование будет сопоставляться с выполняемыми ресурсами в выбранной подписке. Если удалить или переместить подписку или группу ресурсов, резервирование не будет использоваться.  Если указывать общую область, то Azure сопоставляет резервирование с ресурсами, работающими во всех подписках в контексте выставления счетов. Контекст выставления счетов зависит от подписки, использованной для покупки резервирования. Вы можете выбрать область при покупке или изменить ее в любое время после покупки. Дополнительные сведения см. в разделе [Изменение области для резервирования](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

Чтобы программно изменить область, используйте API [обновления резервирования](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Подробнее...

- [Общие сведения об Azure Reserved VM Instances](billing-save-compute-costs-reservations.md)
- [Сведения о применении скидки к зарезервированному экземпляру виртуальной машины Azure](billing-understand-vm-reservation-charges.md)
- [Сведения о применении скидки на программное обеспечение SUSE Linux Enterprise](billing-understand-suse-reservation-charges.md)
- [Сведения о применении скидки на другие резервирования](billing-understand-reservation-charges.md)
- [Общие сведения об использовании резервирования Azure для подписки с оплатой по мере использования](billing-understand-reserved-instance-usage.md)
- [Общие сведения об использовании зарезервированных экземпляров Azure с Соглашением о регистрации Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Затраты на программное обеспечение Windows, которые не включены в стоимость зарезервированных экземпляров Azure](billing-reserved-instance-windows-software-costs.md)
- [Приобретение зарезервированных экземпляров Azure](https://docs.microsoft.com/partner-center/azure-reservations)
