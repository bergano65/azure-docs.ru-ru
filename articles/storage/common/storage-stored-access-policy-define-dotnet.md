---
title: Определите политику сохраненного доступа с помощью .NET - Хранилище Azure
description: Узнайте, как определить политику сохраненного доступа с помощью клиентской библиотеки .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68990745"
---
# <a name="define-a-stored-access-policy-with-net"></a>Определение политики сохраненного доступа с помощью .NET

Политика сохраненного доступа обеспечивает дополнительный уровень контроля над подписями общего доступа на уровне службы (SAS) на стороне сервера. Определение политики сохраненного доступа служит группе общих подписей доступа и предоставляет дополнительные ограничения для подписей общего доступа, которые связаны политикой. Вы можете использовать сохраненную политику доступа, чтобы изменить время начала, время истечения или разрешения для SAS, или отозвать ее после его выдачи.
  
 Хранимые политики доступа поддерживают следующие ресурсы хранилища.  
  
- Контейнеры больших двоичных объектов  
- Общие папки  
- Очереди  
- Таблицы  
  
> [!NOTE]
> Сохраненная политика доступа в контейнере может быть связана с общей подписью доступа, предоставляющей разрешения самому контейнеру или в капли, которые он содержит. Аналогичным образом, сохраненная политика доступа на совместном файле может быть связана с общей подписью доступа, предоставляющей разрешения самой акции или файлам, которые она содержит.  
>
> Политики доступа сохранены поддерживаются только для службы SAS. Политики доступа сохранены не поддерживаются для SAS или делегации пользователей SAS.  

## <a name="create-a-stored-access-policy"></a>Создание хранимой политики доступа

Следующий код создает хранимую политику доступа для контейнера. Политики доступа можно использовать для указания ограничений для SAS службы, накладываемые на контейнер или BLOB-объекты.

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

## <a name="see-also"></a>См. также

- [Предоставляетограниченный доступ к ресурсам хранения Azure с помощью общих подписей доступа (SAS)](storage-sas-overview.md)
- [Определение хранимой политики доступа](/rest/api/storageservices/define-stored-access-policy)

