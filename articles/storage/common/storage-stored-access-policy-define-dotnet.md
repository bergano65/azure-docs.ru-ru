---
title: Определение хранимой политики доступа с помощью .NET — служба хранилища Azure
description: Узнайте, как определить хранимую политику доступа с помощью клиентской библиотеки .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990745"
---
# <a name="define-a-stored-access-policy-with-net"></a>Определение хранимой политики доступа с помощью .NET

Хранимая политика доступа предоставляет дополнительный уровень контроля над ПОДПИСАНными URL-адресами уровня службы на стороне сервера. Определение хранимой политики доступа служит для группировки подписанных URL-групп и предоставления дополнительных ограничений для подписанных URL-подписей, привязанных к политике. Хранимую политику доступа можно использовать для изменения времени запуска, времени окончания срока действия или разрешений для SAS, а также для отмены ее после ее выдачи.
  
 Следующие ресурсы хранилища поддерживают хранимые политики доступа:  
  
- Контейнеры больших двоичных объектов  
- Общие папки  
- Списки ожидания  
- Таблицы  
  
> [!NOTE]
> Хранимую политику доступа в контейнере можно связать с подписанным URL-путем, предоставив разрешения для самого контейнера или содержащихся в нем больших двоичных объектов. Аналогичным образом хранимая политика доступа в общей папке может быть связана с подписанным URL-путем, предоставляющим разрешения для самой общей папки или содержащихся в ней файлов.  
>
> Хранимые политики доступа поддерживаются только для SAS службы. Хранимые политики доступа не поддерживаются для SAS учетной записи или SAS делегирования пользователя.  

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

- [Предоставление ограниченного доступа к ресурсам службы хранилища Azure с помощью подписанных URL-адресов (SAS)](storage-sas-overview.md)
- [Определение хранимой политики доступа](/rest/api/storageservices/define-stored-access-policy)

