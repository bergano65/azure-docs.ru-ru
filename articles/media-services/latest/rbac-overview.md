---
title: Контроль доступа на основе ролей для учетных записей медиаслужб - Azure Документы Майкрософт
description: В этой статье обсуждается элемент управления доступом на основе ролей (RBAC) для учетных записей медиаслужб Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66236923"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Контроль доступа на основе ролей (RBAC) для учетных записей Медиа-услуг

В настоящее время служба мультимедиа Azure не определяет пользовательские роли, характерные для службы. Чтобы получить полный доступ к учетной записи Media Services, клиенты могут использовать встроенные роли **Владельца** или **Участника.** Основное различие между этими ролями: **Владелец** может контролировать, кто имеет доступ к ресурсу, а **автор** не может. Встроенная роль Reader также может быть использована, но пользователь или приложение будет иметь только доступ к AA- службы **мультимедиа.** 

## <a name="design-principles"></a>Принципы проектирования

Один из ключевых принципов проектирования API версии 3 — сделать API более безопасным. aIS v3 не возвращают секреты или учетные данные в операциях **Get** или **List.** Ключи всегда являются NULL, пустыми или исключенными из ответа. Пользователь должен вызвать отдельный метод действий, чтобы получить секреты или учетные данные. Роль **читателя** не может вызывать такие операции, как Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Наличие отдельных действий позволяет при желании устанавливать более детальные разрешения на безопасность RBAC в пользовательской роли.

Чтобы перечислить поддержки медиаслужб, сделайте:

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

Встроенная статья [определения ролей](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) говорит вам, что именно дает роль. 

Дополнительные сведения см. в этих статьях:

- [Роли классического администратора подписки, роли RBAC Azure и роли администратора Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Что такое RBAC для ресурсов Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Используйте RBAC для управления доступом](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Операции поставщика ресурсов медиауслуг](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Дальнейшие действия

- [Разработка с помощью медиа-сервисов v3 AIS](media-services-apis-overview.md)
- [Получите политику ключей контента с помощью Media Services .NET](get-content-key-policy-dotnet-howto.md)
