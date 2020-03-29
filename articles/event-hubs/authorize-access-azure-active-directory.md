---
title: Авторизация доступа с помощью Azure Active Directory
description: В этой статье содержится информация об авторизации доступа к ресурсам событийных концентров с помощью Active Directory Azure.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 1fa8f7a48c03ead7e939185b23834b3049b3e21c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064863"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Разрешить доступ к ресурсам концентраторов событий с помощью активного каталога Azure
Концентраторы событий Azure поддерживают использование active Directory Azure (Azure AD) для авторизации запросов к ресурсам концентраторов событий. С Помощью Azure AD можно использовать элементуправления доступа на основе ролей (RBAC) для предоставления разрешений директору службы безопасности, который может быть пользователем, или директором службы приложения. Чтобы узнать больше о ролях и назначениях ролей, [см.](../role-based-access-control/overview.md)

## <a name="overview"></a>Обзор
Когда директор безопасности (пользователь или приложение) пытается получить доступ к ресурсу Event Hubs, запрос должен быть авторизован. С Azure AD доступ к ресурсу является двухэтапным процессом. 

 1. Во-первых, личность директора службы безопасности проверяется, и возвращается токен OAuth 2.0. Имя ресурса для запроса `https://eventhubs.azure.net/`маркера находится под названием . Для клиентов Kafka ресурс для запроса токена . `https://<namespace>.servicebus.windows.net`
 1. Далее токен передается в рамках запроса в службу концентраторов событий для авторизации доступа к указанному ресурсу.

Шаг проверки подлинности требует, чтобы запрос приложения содержит токен доступа OAuth 2.0 во время выполнения. Если приложение работает в рамках сущности Azure, такой как Azure VM, набор виртуальной шкалы машин или приложение Azure Function, оно может использовать управляемое удостоверение для доступа к ресурсам. Чтобы узнать, как проверить подлинность запросов, сделанных управляемым идентификатором в службе концентраторов событий, свяжетесь с [authenticate доступ к ресурсам Azure Event Hubs с помощью Active Directory Azure и управляемых идентификаторов для ресурсов Azure.](authenticate-managed-identity.md) 

Шаг авторизации требует, чтобы одна или несколько ролей RBAC были назначены директору безопасности. Концентраторы событий Azure предоставляют роли RBAC, охватывающие наборы разрешений для ресурсов концентраторов событий. Роли, назначенные директору безопасности, определяют разрешения, которые будет иметь принцип. Для получения дополнительной информации о ролях RBAC смотрите [встроенные роли RBAC для концентраторов Azure Event.](#built-in-rbac-roles-for-azure-event-hubs) 

Родные приложения и веб-приложения, которые делают запросы в концентраторы событий, также могут авторизоваться с Помощью Azure AD. Чтобы узнать, как запросить токен доступа и использовать его для авторизации запросов для ресурсов событийных концентраторов, смотрите [доступ Authenticate к концентрам Azure Event с Azure AD из приложения.](authenticate-application.md) 

## <a name="assign-rbac-roles-for-access-rights"></a>Назначать роли RBAC для прав доступа
Azure Active Directory (Azure AD) разрешает права доступа к защищенным ресурсам с помощью [управления доступом на основе ролей (RBAC)](../role-based-access-control/overview.md). Концентраторы событий Azure определяют набор встроенных ролей RBAC, которые охватывают общие наборы разрешений, используемых для доступа к данным концентратора событий, и можно также определить пользовательские роли для доступа к данным.

Когда роль RBAC назначается директору безопасности Azure AD, Azure предоставляет доступ к этим ресурсам для этого принципа безопасности. Доступ может быть доступен на уровне подписки, группы ресурсов, пространства имен событий концентраторов или любого ресурса под ним. Принципом безопасности Azure AD может быть пользователь, директор службы приложений или [управляемый интимент для ресурсов Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Встроенные роли RBAC для концентраторов событий Azure
Azure предоставляет следующие встроенные роли RBAC для авторизации доступа к данным событийных концентраторов с помощью Azure AD и OAuth:

- [Владелец данных Azure Event Hubs:](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)Используйте эту роль, чтобы предоставить полный доступ к ресурсам концентраторов событий.
- [Отправитель данных концентранов Azure Event:](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)Используйте эту роль, чтобы предоставить доступ к ресурсам концентраторов событий.
- [Получатель данных Azure Event Hubs:](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)Используйте эту роль, чтобы предоставить доступ к ресурсам концентраторов событий.

## <a name="resource-scope"></a>Область ресурсов 
Прежде чем назначить роль RBAC директору безопасности, определите область доступа, которую должен иметь директор службы безопасности. Рекомендации по практике диктуют, что всегда лучше предоставить только максимально узкую область.

В следующем списке описаны уровни, на которых можно получить доступ к ресурсам Event Hubs, начиная с самого узкого объема:

- **Группа потребителей**: В этом диапазоне назначение ролей применяется только к этой сущности. В настоящее время портал Azure не поддерживает назначение роли RBAC директору безопасности на этом уровне. 
- **Концентратор событий**: Назначение ролей применяется к объекту концентратора событий и группе потребителей под ним.
- **Namespace**: Назначение ролей охватывает всю топологию концентраторов событий под пространством имен и группу потребителей, связанную с ним.
- **Группа ресурсов**: Назначение ролей применяется ко всем ресурсам концентраторов событий в группе ресурсов.
- **Подписка**: Назначение ролей распространяется на все ресурсы концентраторов событий во всех группах ресурсов в подписке.

> [!NOTE]
> - Имейте в виду, что распространение заданий ролей RBAC может занять до пяти минут. 
> - Это содержание относится как к концентратам событий, так и кцентратам событий для Apache Kafka. Для получения дополнительной информации о концентрах событий для поддержки Kafka [см.](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication)


Для получения дополнительной [информации](../role-based-access-control/role-definitions.md#management-and-data-operations)о том, как определяются встроенные роли, см. Для получения информации о создании пользовательских ролей RBAC [см.](../role-based-access-control/custom-roles.md)



## <a name="samples"></a>Примеры
- [Образцы microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    В этих образцах используется старая библиотека **Microsoft.Azure.EventHubs,** но вы можете легко обновить ее до самой последней библиотеки **Azure.Messaging.EventHubs.** Для перемещения образца из старой библиотеки в новую можно [см.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)
- [Образцы образцов Azure.Messaging.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Этот пример был обновлен для использования последней библиотеки **Azure.Messaging.EventHubs.**
- [Концентраторы событий для образцов Кафки - OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth). 


## <a name="next-steps"></a>Дальнейшие действия
- Узнайте, как назначить встроенную роль RBAC главному директору безопасности, [см.](authenticate-application.md)
- [Узнайте, как создавать пользовательские роли с помощью RBAC.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)
- [Узнайте, как использовать активный каталог Azure с EH](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Смотрите следующие статьи:

- [Authenticate requests to Azure Event Hubs из приложения с использованием active-каталога Azure](authenticate-application.md)
- [Проверка управляемого итогового данных с помощью Active Directory Azure для доступа к ресурсам концентратов событий](authenticate-managed-identity.md)
- [Проверка подлинности концентрах событий Azure с использованием общих подписей доступа](authenticate-shared-access-signature.md)
- [Разрешить доступ к ресурсам концентраторов событий с помощью общих подписей доступа](authorize-access-shared-access-signature.md)
