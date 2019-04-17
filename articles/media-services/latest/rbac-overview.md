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
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 82c438ea246ba18fa7dac2281d68a3ea7ba41db8
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617722"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Элемент управления доступом на основе ролей (RBAC) для учетных записей служб мультимедиа

В настоящее время службы мультимедиа Azure не определяет все пользовательские роли, определенные в службу. Чтобы получить полный доступ к учетной записи служб мультимедиа, клиенты могут использовать встроенные роли **владельца** или **участник**. Основное различие между этими ролями является: **владельца** можете контролировать доступ к ресурсу и **участник** невозможно. Встроенная **чтения** роли также можно использовать, но пользователь или приложение будет иметь только доступ на чтение к API служб мультимедиа. 

## <a name="design-principles"></a>Принципы проектирования

Один из ключевых принципов проектирования API версии 3 — сделать API более безопасным. API-интерфейсы v3 не возвращают секретные данные или учетные данные на **получить** или **списка** операций. Ключи всегда являются NULL, пустыми или исключенными из ответа. Пользователь должен вызвать метод отдельное действие для получения секретов или учетные данные. **Чтения** роли нельзя вызывать операции, такие как Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets. Наличие отдельных действий позволяет при необходимости задайте более детализированных разрешений системы безопасности RBAC в пользовательскую роль.

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
