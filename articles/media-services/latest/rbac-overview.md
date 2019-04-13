---
title: Управление доступом на основе ролей для учетных записей служб мультимедиа — Azure | Документация Майкрософт
description: В этой статье рассматриваются управления доступом на основе ролей (RBAC) для учетных записей служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f72e98d8874a5a5dc94deb882affdf66388b13c9
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548531"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Элемент управления доступом на основе ролей (RBAC) для учетных записей служб мультимедиа

В настоящее время службы мультимедиа Azure не определен какой-либо конкретной пользовательские роли в службу. Клиенты могут использовать встроенные роли **владельца** или **участник** получить полный доступ к учетной записи служб мультимедиа. Основное различие между этими ролями является: **владельца** можете контролировать доступ к ресурсу и **участник** невозможно. Встроенное средство чтения учетной записи имеет только доступ на чтение для учетной записи служб мультимедиа. 

## <a name="design-principles"></a>Принципы проектирования

Один из ключевых принципов проектирования API версии 3 — сделать API более безопасным. API-интерфейсы v3 не возвращают секретные данные или учетные данные на **получить** или **списка** операций. Ключи всегда являются NULL, пустыми или исключенными из ответа. Пользователь должен вызвать метод отдельное действие для получения секретов или учетные данные. **Чтения** роли не может вызывать операции, поэтому не может вызвать операции, такие как ContentKeyPolicies.GetPolicyPropertiesWithSecrets Asset.ListContainerSas StreamingLocator.ListContentKeys,. Наличие отдельных действий позволяет при необходимости задайте более детализированных разрешений системы безопасности RBAC в пользовательскую роль.

Чтобы получить список операций служб мультимедиа поддерживает, выполните действия.

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[Определения встроенной роли](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) статья расскажет вам, именно то, что эта роль предоставляет. 

Дополнительные сведения см. в этих статьях:

- [Роли классического администратора подписки, роли RBAC Azure и роли администратора Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Что такое RBAC для ресурсов Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Использование RBAC для управления доступом](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)

## <a name="next-steps"></a>Дальнейшие действия

- [Разработка с использованием служб мультимедиа версии 3 API-интерфейсов](media-services-apis-overview.md)
- [Получение ключа политики содержимого, с помощью служб мультимедиа для .NET](get-content-key-policy-dotnet-howto.md)
