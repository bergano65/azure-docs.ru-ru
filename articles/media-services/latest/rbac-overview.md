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
ms.openlocfilehash: c75b6e67932cfd26a3374eab3f3efa34ceade577
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504489"
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

В статье [встроенные определения ролей](../../role-based-access-control/built-in-roles.md) содержатся сведения о том, что предоставляет роль. 

Дополнительные сведения см. в этих статьях:

- [Роли администратора классической подписки, роли Azure и роли администратора Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Что такое управление доступом на основе ролей в Azure (RBAC)?](../../role-based-access-control/overview.md)
- [Использование RBAC для управления доступом](../../role-based-access-control/role-assignments-rest.md)
- [Операции поставщика ресурсов служб мультимедиа](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>Дальнейшие действия

- [Разработка с помощью API-интерфейсов служб мультимедиа v3](media-services-apis-overview.md)
- [Получение политики ключей содержимого с помощью служб мультимедиа .NET](get-content-key-policy-dotnet-howto.md)
