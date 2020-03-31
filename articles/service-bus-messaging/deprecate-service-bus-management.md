---
title: Службы обмена сообщениями Azure - Менеджер службы для менеджера ресурсов
description: В этой статье представлено отображение дефеката менеджера службы Rest API & Смлетов PowerShell на ресурсный менеджер REST API & смдлетов PowerShell.
services: service-bus-messaging, event-hubs, event-grid
documentationcenter: na
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2020
ms.author: spelluru
ms.openlocfilehash: d263381667319b98a28ee6168e2de75c4041b58a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589913"
---
# <a name="deprecation-of-azure-service-manager-support-for-azure-service-bus-relay-and-event-hubs"></a>Изнурение службы менеджера Azure для автобусов, ретранслятов и событийных концентраторов Azure

Менеджер ресурсов, наш стек облачной инфраструктуры нового поколения, полностью заменяет «классическую» модель управления службами Azure (классическая модель развертывания). В результате классическая модель развертывания REST AIS и поддержка сервисных автобусов, ретрансляций и концентраторов событий будут выведены из эксплуатации 1 ноября 2021 года. Это амортизацию было впервые объявлено на [Microsoft Tech Community объявление](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Deprecating-Service-Management-support-for-Azure-Service-Bus/ba-p/370909), но мы недавно решили продлить период амортизм еще два года с момента первоначального объявления. Для легкой идентификации `management.core.windows.net` эти AA имеют в своих URI. Обратитесь к следующей таблице для списка утерянных API и их aPI-версии API-менеджера ресурсов Azure, которую теперь следует использовать.

Чтобы продолжить использование сервисных автобусов, ретрансляций и концентраторов событий, перейдите на управление ресурсами до 31 октября 2021 года. Мы призываем всех клиентов, которые все еще используют старые AI- исключать в ближайшее время, воспользоваться дополнительными преимуществами Resource Manager, которые включают группировку ресурсов, теги, упрощенный процесс развертывания и управления, а также мелкозернистый доступ контроль с помощью управления доступом на основе ролей (RBAC).

Для получения дополнительной информации о менеджере ресурсов [TechNet Blog](https://blogs.technet.microsoft.com/meamcs/2016/12/22/difference-between-azure-service-manager-and-azure-resource-manager/)Azure против менеджера службы Azure см.

Для получения дополнительной информации об API-менеджере службы и менеджере ресурсов для концентраторов службы Azure, Ретрансляции и событий см.

- [Сервисный автобус Azure](/rest/api/servicebus/)
- [Концентраторы событий Azure](/rest/api/eventhub/)
- [Ретранслятор Azure](/rest/api/relay/)

## <a name="service-manager-rest-api---resource-manager-rest-api"></a>Менеджер службы REST API - Менеджер ресурсов REST API

| AA-аДИ менеджера обслуживания (Deprecated) | Менеджер ресурсов - Service Bus API | Менеджер ресурсов - API концентратора событий | Менеджер ресурсов - API ретрансляции |
| --------------- | ----------------- | ----------------- | ----------------- | 
| **Namespaces-GetNamespaceAsync** <br/>[Сервисный автобус Получить Namespace](/rest/api/servicebus/get-namespace)<br/>[Концентратор событий получить Namespace](/rest/api/eventhub/get-event-hub)<br/>[Реле Получить Namespace](/rest/api/servicebus/get-relays)<br/> ```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **СоединениеПодробности-GetConnectionDetails**<br/>Сервисный автобус/концентратор событий/реле GetConnectionDetals<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/ConnectionDetails``` | [listkeys](/rest/api/servicebus/namespaces/listkeys) | [listkeys](/rest/api/eventhub/namespaces/listkeys) | [listkeys](/rest/api/relay/namespaces/listkeys) |
| **Темы-GetTopicsAsync**<br/>Служебная шина<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics? $skip={skip}&$top={top}``` | [Список](/rest/api/servicebus/topics/listbynamespace) | &nbsp; | &nbsp; | 
| **Очереди-GetКизиасин** <br/>Служебная шина<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/queues/{queueName}``` | [get](/rest/api/servicebus/queues/get) | &nbsp; | &nbsp; | 
| **Эстафеты-GetRelaysAsync**<br/>[Получить реле](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/relays? $skip={skip}&$top={top}```| &nbsp; | &nbsp; | [Список](/rest/api/relay/wcfrelays/listbynamespace) | 
| **NamespaceАвторизация-GetNamespaceАвторизацияРулеАсин**<br/>Сервисный автобус/концентратор событий/реле GetNamespaceAuthRule<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/authorizationrules?``` | [получитьавторизацию](/rest/api/servicebus/namespaces/getauthorizationrule) |[получитьавторизацию](/rest/api/eventhub/namespaces/getauthorizationrule) | [получитьавторизацию](/rest/api/relay/namespaces/getauthorizationrule) |
| **Namespaces-DeleteNameSpaceAsync**<br/>[Сервис автобус Удалить Namespace](/rest/api/servicebus/delete-namespace)<br/>[Концентраторы событий Удаляются в пространстве имен](/rest/api/eventhub/delete-event-hub)<br/>[Ретрансляции Удалить Namespace](/rest/api/servicebus/delete-namespace)<br/> ```DELETE  https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [Удалить](/rest/api/servicebus/namespaces/delete) | [Удалить](/rest/api/eventhub/namespaces/delete) | [Удалить](/rest/api/relay/namespaces/delete) | 
| **СообщенияСКУПлан-Гетпланасинк**<br/>Сервисный автобус/концентратор событий/реле Получить Namespace<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **СообщенияSKUPlan-UpdatePlanAsync**<br/>Сервисный автобус/концентратор событий/реле Получить Namespace<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/MessagingPlan``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **NamespaceАвторизацияПравила-ОбновлениеNamespaceАвторизацияРулеАсин**<br/>Сервисный автобус/концентратор событий/реле Получить Namespace<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) | 
| **NamespaceАвторизация-СозданиеNamespaceAuthorizationAsync**<br/> 
Сервисный автобус/концентратор событий/эстафета<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` |[createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdateauthorizationrule](/rest/api/eventhub/namespaces/createorupdateauthorizationrule) | [createorupdateauthorizationrule](/rest/api/relay/namespaces/createorupdateauthorizationrule) |
| **NamespaceProperties-GetNamespacePropertiesAsync**<br/>[Сервисный автобус Получить Namespace](/rest/api/servicebus/get-namespace)<br/>[Концентраторы событий получают namespace](/rest/api/eventhub/get-event-hub)<br/>[Реле Получить Namespace](/rest/api/servicebus/get-relays)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [get](/rest/api/servicebus/namespaces/get) | [get](/rest/api/eventhub/namespaces/get) | [get](/rest/api/relay/namespaces/get) |
| **RegionCodes-GetRegionCodesAsync**<br/>Сервисный автобус/EventHub/Relay Получить Namespace<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [listbysku](/rest/api/servicebus/regions/listbysku) | [listbysku](/rest/api/eventhub/regions/listbysku) | &nbsp; | 
| **NamespaceProperties-UpdateNamespacePropertyAsync**<br/>Сервисный автобус/EventHub/Эстафета<br/>```GET    https://management.core.windows.net/{subscription ID}/services/ServiceBus/Regions/``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) |
| **EventHubsCrud-ListEventHubsAsync**<br/>[Список концентраторов событий](/rest/api/eventhub/list-event-hubs)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs?$skip={skip}&$top={top}``` | &nbsp; | [Список](/rest/api/eventhub/eventhubs/listbynamespace) | &nbsp; | 
| **EventHubsCrud-GetEventHubAsync**<br/>[Получить концентраторы событий](/rest/api/eventhub/get-event-hub)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/eventhubs/{eventHubPath}``` | &nbsp; | [get](/rest/api/eventhub/eventhubs/get) | &nbsp; | 
| **NamespaceАвторизация-DeleteNamespaceАвторизацияРулеАсин**<br/>Сервисный автобус/концентратор событий/эстафета<br/>```DELETE https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules/{rule name}``` | [deleteauthorizationrule](/rest/api/servicebus/namespaces/deleteauthorizationrule) | [deleteauthorizationrule](/rest/api/eventhub/namespaces/deleteauthorizationrule) | [deleteauthorizationrule](/rest/api/relay/namespaces/deleteauthorizationrule) |
| **NamespaceАвторизация-GetNamespaceAuthorizationAsync**<br/>Сервисный автобус/EventHub/Эстафета<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/AuthorizationRules``` | [listauthorizationrules](/rest/api/servicebus/namespaces/listauthorizationrules) | [listauthorizationrules](/rest/api/eventhub/namespaces/listauthorizationrules) | [listauthorizationrules](/rest/api/relay/namespaces/listauthorizationrules) |
| **NamespaceAvailability-IsNamespaceAvailable**<br/>[Доступность наименного пространства службы](/rest/api/servicebus/check-namespace-availability)<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/CheckNamespaceAvailability/?namespace=<namespaceValue>``` | [checknameavailability](/rest/api/servicebus/namespaces/checknameavailability) | [checknameavailability](/rest/api/eventhub/namespaces/checknameavailability) | [checknameavailability](/rest/api/relay/namespaces/checknameavailability) |
| **Namespaces-CreateOrUpdateNameSpaceAsync**<br/>Сервисный автобус/концентратор событий/эстафета<br/>```PUT https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}``` | [createorupdate](/rest/api/servicebus/namespaces/createorupdate) | [createorupdate](/rest/api/eventhub/namespaces/createorupdate) | [createorupdate](/rest/api/relay/namespaces/createorupdate) | 
| **Темы-GetTopicAsync**<br/>```GET https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/topics/{topicPath}``` | [get](/rest/api/servicebus/topics/get) | &nbsp; | &nbsp; |

## <a name="service-manager-powershell---resource-manager-powershell"></a>Менеджер по обслуживанию PowerShell - менеджер по ресурсам PowerShell
| Команда менеджера службы PowerShell (обезображено) | Новые команды менеджера ресурсов | Команда менеджера ресурсов новее |
| ----- | ----- | ----- | 
| [Get-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusAuthorizationRule.](/powershell/module/azurerm.servicebus/get-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Get-AzServiceBusАвториацияРуле](/powershell/module/az.servicebus/get-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Get-AzureSBLocation](/powershell/module/servicemanagement/azure/get-azuresblocation?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusGeoDRConfiguration](/powershell/module/azurerm.servicebus/get-azurermservicebusgeodrconfiguration?view=azurermps-6.13.0) | [Get-AzServiceBusGeoDRConfiguration](/powershell/module/az.servicebus/get-azservicebusgeodrconfiguration?view=azps-1.6.0) |
| [Get-AzureSBNamespace](/powershell/module/servicemanagement/azure/get-azuresbnamespace?view=azuresmps-4.0.0) | [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace?view=azurermps-6.13.0) | [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace?view=azps-1.6.0) |
| [New-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/new-azuresbauthorizationrule?view=azuresmps-4.0.0) | [New-AzureRmServiceBusAuthorizationRule.](/powershell/module/azurerm.servicebus/new-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Новый-AzServiceBusАвтористом](/powershell/module/az.servicebus/new-azservicebusauthorizationrule?view=azps-1.6.0) |
| [New-AzureSBNamespace](/powershell/module/servicemanagement/azure/new-azuresbnamespace?view=azuresmps-4.0.0) | [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace?view=azurermps-6.13.0) | [Новое-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace?view=azps-1.6.0) |
| [Удалить-AzureRmRelayАвторизацияРуле](/powershell/module/azurerm.relay/remove-azurermrelayauthorizationrule?view=azurermps-6.13.0) | [Remove-AzureRmEventHubAuthorizationRule](/powershell/module/azurerm.eventhub/remove-azurermeventhubauthorizationrule?view=azurermps-6.13.0) | [Удалить-AzServiceBusАвторитив](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule?view=azps-1.6.0) |
| [Remove-AzureSBNamespace](/powershell/module/servicemanagement/azure/remove-azuresbnamespace?view=azuresmps-4.0.0) | [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace?view=azurermps-6.13.0) | [Удалить-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace?view=azps-1.6.0) |
| [Set-AzureSBAuthorizationRule](/powershell/module/servicemanagement/azure/set-azuresbauthorizationrule?view=azuresmps-4.0.0) | [Set-AzureRmServiceBusAuthorizationRule.](/powershell/module/azurerm.servicebus/set-azurermservicebusauthorizationrule?view=azurermps-6.13.0) | [Set-AzServiceBusАвториацияРуле](/powershell/module/az.servicebus/set-azservicebusauthorizationrule?view=azps-1.6.0) |

## <a name="next-steps"></a>Дальнейшие действия
Обратитесь к следующей документации. 

- Последняя документация REST API
    - [Сервисный автобус Azure](/rest/api/servicebus/)
    - [Концентраторы событий Azure](/rest/api/eventhub/)
    - [Ретранслятор Azure](/rest/api/relay/)
- Последняя документация PowerShell
    - [Сервисный автобус Azure](/powershell/module/azurerm.servicebus/?view=azurermps-6.13.0#service_bus)
    - [Концентраторы событий Azure](/powershell/module/azurerm.eventhub/?view=azurermps-6.13.0#event_hub)
    - [Сетка событий Azure](/powershell/module/azurerm.eventgrid/?view=azurermps-6.13.0#event_grid)
