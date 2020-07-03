---
title: Управление доступом на основе ролей для учетных записей служб мультимедиа в Azure | Документация Майкрософт
description: В этой статье рассматривается управление доступом на основе ролей (RBAC) для учетных записей служб мультимедиа Azure.
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/26/2020
ms.locfileid: "66236923"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Управление доступом на основе ролей (RBAC) для учетных записей служб мультимедиа

Сейчас службы мультимедиа Azure не определяют никаких пользовательских ролей, относящихся к службе. Чтобы получить полный доступ к учетной записи служб мультимедиа, клиенты могут использовать встроенные роли **владельца** или **участника**. Основное различие между этими ролями состоит в том, что **владелец** может контролировать доступ к ресурсу, а **участник** — нет. Также можно использовать встроенную роль **читателя** , но пользователь или приложение будут иметь доступ только для чтения к API-интерфейсам служб мультимедиа. 

## <a name="design-principles"></a>Принципы проектирования

Один из ключевых принципов проектирования API версии 3 — сделать API более безопасным. API V3 не возвращают секреты или учетные данные при операциях **Get** или **List** . Ключи всегда являются NULL, пустыми или исключенными из ответа. Пользователю необходимо вызвать отдельный метод действия для получения секретов или учетных данных. Роль **читателя** не может вызывать такие операции, как Asset. Листконтаинерсас, Стреаминглокатор. Листконтенткэйс, КонтенткэйполиЦиес. жетполиципропертиесвиссекретс. Наличие отдельных действий позволяет при необходимости задать более детализированные разрешения безопасности RBAC в пользовательской роли.

Чтобы получить список поддерживаемых служб мультимедиа, выполните следующие действия.

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

В статье [встроенные определения ролей](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) содержатся сведения о том, что предоставляет роль. 

Дополнительные сведения см. в этих статьях:

- [Роли классического администратора подписки, роли RBAC Azure и роли администратора Azure AD](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Что такое RBAC для ресурсов Azure?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Использование RBAC для управления доступом](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Операции поставщика ресурсов служб мультимедиа](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>Следующие шаги

- [Разработка с помощью API-интерфейсов служб мультимедиа v3](media-services-apis-overview.md)
- [Получение политики ключей содержимого с помощью служб мультимедиа .NET](get-content-key-policy-dotnet-howto.md)
