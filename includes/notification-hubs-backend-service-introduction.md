---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 98c9d83424e7d347bb6e1166a3b00304d39e0cce
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095453"
---
Серверная часть [веб-API ASP.NET Core](https://dotnet.microsoft.com/apps/aspnet/apis) используется для обработки [регистрации устройств](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) для клиента с использованием [этого подхода](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#installations). Служба также будет отправлять push-уведомления с использованием кроссплатформенного механизма. 

Эти операции обрабатываются с помощью [пакета средств разработки Центров уведомлений для внутренних операций](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). См. раздел [Управление регистрацией из серверной части](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#registration-management-from-a-backend).
