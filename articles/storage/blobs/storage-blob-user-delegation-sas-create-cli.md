---
title: Создание SAS для делегирования пользователя для контейнера или большого двоичного объекта с помощью Azure CLI (Предварительная версия) в службе хранилища Azure
description: Узнайте, как создать SAS делегирования пользователя с помощью учетных данных Azure Active Directory в службе хранилища Azure с помощью Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: ef51a1b130323a8799d5334d8d043fda08fcc7ef
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69896964"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli-preview"></a>Создание SAS для делегирования пользователя для контейнера или большого двоичного объекта с Azure CLI (Предварительная версия)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

В этой статье показано, как использовать учетные данные Azure Active Directory (Azure AD) для создания SAS делегирования пользователя для контейнера или большого двоичного объекта с Azure CLI (Предварительная версия).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Установите последнюю версию Azure CLI

Чтобы использовать Azure CLI для защиты SAS с помощью учетных данных Azure AD, сначала убедитесь, что установлена последняя версия Azure CLI. Дополнительные сведения об установке Azure CLI см. в разделе [установка Azure CLI](/cli/azure/install-azure-cli).

## <a name="sign-in-with-azure-ad-credentials"></a>Вход с использованием учетных данных Azure AD

Войдите в Azure CLI с учетными данными Azure AD. Дополнительные сведения см. в разделе [Вход с помощью Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-rbac"></a>Назначение разрешений с помощью RBAC

Чтобы создать SAS для делегирования пользователей из Azure PowerShell, учетной записи Azure AD, используемой для входа в Azure CLI, должна быть назначена роль, включающая действие **Microsoft. Storage/storageAccounts/блобсервицес/женератеусерделегатионкэй** . Это разрешение позволяет этой учетной записи Azure AD запрашивать *ключ делегирования пользователя*. Ключ делегирования пользователя используется для подписи SAS делегирования пользователя. Роль, предоставляющая действие **Microsoft. Storage/storageAccounts/блобсервицес/женератеусерделегатионкэй** , должна быть назначена на уровне учетной записи хранения, группы ресурсов или подписки.

Если у вас нет достаточных разрешений для назначения ролей RBAC субъекту безопасности Azure AD, может потребоваться попросить владельца или администратора учетной записи назначить необходимые разрешения.

В следующем примере назначается роль **участника данных BLOB-объекта хранилища** , которая включает действие **Microsoft. Storage/storageAccounts/блобсервицес/женератеусерделегатионкэй** . Роль ограничивается уровнем учетной записи хранения.

Не забудьте заменить значения заполнителей в угловых скобках собственными значениями:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Дополнительные сведения о встроенных ролях, включающих действие **Microsoft. Storage/storageAccounts/блобсервицес/женератеусерделегатионкэй** , см. в статье [встроенные роли для ресурсов Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Использование учетных данных Azure AD для защиты SAS

При создании SAS делегирования пользователя с Azure CLI ключ делегирования пользователя, используемый для подписи SAS, создается неявно. Время начала и окончания срока действия, заданное для SAS, также используются в качестве времени начала и срока действия для ключа делегирования пользователя.

Так как максимальный интервал, по истечении которого ключ делегирования пользователя является допустимым, — 7 дней с даты начала, необходимо указать время окончания срока действия для SAS в течение 7 дней с момента запуска. После истечения срока действия ключа делегирования пользователя SAS является недействительным, поэтому срок действия SAS с временем окончания срока хранения более 7 дней будет действительным только в течение 7 дней.

При создании SAS `--auth-mode login` для делегирования пользователей требуются и `--as-user parameters` . Укажите *имя входа* для `--auth-mode` параметра, чтобы запросы, выполненные в службе хранилища Azure, были разрешены с учетными данными Azure AD. `--as-user` Укажите параметр, чтобы указать, что возвращаемые SAS должны быть подписаны на делегирование пользователя.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Создание SAS для делегирования пользователя для контейнера

Чтобы создать SAS для делегирования пользователей для контейнера с Azure CLI, вызовите команду [AZ Storage Container Generate-SAS](/cli/azure/storage/container#az-storage-container-generate-sas) .

Поддерживаются следующие разрешения для сопоставления безопасности делегирования пользователей в контейнере: Добавление, создание, удаление, перечисление, чтение и запись. Разрешения можно указать отдельно или в сочетании. Дополнительные сведения об этих разрешениях см. [в разделе Создание SAS для делегирования пользователей](/rest/api/storageservices/create-user-delegation-sas).

В следующем примере возвращается маркер SAS для делегирования пользователя для контейнера. Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

Возвращаемый токен SAS для делегирования пользователя будет выглядеть следующим образом:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Создание SAS делегирования пользователя для большого двоичного объекта

Чтобы создать SAS делегирования пользователя для большого двоичного объекта с Azure CLI, вызовите команду [AZ Storage BLOB Generate-SAS](/cli/azure/storage/blob#az-storage-blob-generate-sas) .

Поддерживаются следующие разрешения для сопоставления безопасности делегирования пользователей в большом двоичном объекте: Добавление, создание, удаление, чтение и запись. Разрешения можно указать отдельно или в сочетании. Дополнительные сведения об этих разрешениях см. [в разделе Создание SAS для делегирования пользователей](/rest/api/storageservices/create-user-delegation-sas).

Следующий синтаксис возвращает SAS делегирования пользователя для большого двоичного объекта. В примере указывается `--full-uri` параметр, который возвращает URI большого двоичного объекта с добавленным маркером SAS. Не забудьте заменить значения заполнителей в квадратных скобках собственными значениями:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
    --full-uri
```

Возвращаемый URI SAS для делегирования пользователя будет выглядеть примерно так:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> SAS пользователя, поддерживающий делегирование, не поддерживает определение разрешений с помощью хранимой политики доступа.

## <a name="next-steps"></a>Следующие шаги

- [Создание SAS для делегирования пользователей (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Операция получения ключа делегирования пользователя](/rest/api/storageservices/get-user-delegation-key)
