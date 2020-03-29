---
title: Используйте Azure CLI для создания SAS-делегации пользователя для контейнера или капли
titleSuffix: Azure Storage
description: Узнайте, как создать SAS-делегации пользователей с помощью учетных данных Active Directory Azure с помощью Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: e1a81b25042501a166cee122279d21e3702cd419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371995"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Создание делегации пользователя SAS для контейнера или капли с помощью Azure CLI

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

В этой статье показано, как использовать учетные данные Active Directory (Azure AD) для создания SAS-сообщества пользователей для контейнера или капли с Azure CLI.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Установите последнюю версию Azure CLI

Чтобы использовать Azure CLI для обеспечения sAS с учетными данными Azure AD, сначала убедитесь, что вы установили последнюю версию Azure CLI. Для получения дополнительной информации об установке Azure CLI [см.](/cli/azure/install-azure-cli)

Чтобы создать делегацию пользователей SAS с помощью Azure CLI, убедитесь, что вы установили версию 2.0.78 или позже. Чтобы проверить установленную версию, используйте `az --version` команду.

## <a name="sign-in-with-azure-ad-credentials"></a>Войти в систему с учетными данными Azure AD

Вопийте в Azure CLI с вашими учетными данными Azure AD. Дополнительные сведения см. в разделе [Вход с помощью Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-rbac"></a>Назначать разрешения с RBAC

Для создания делегации пользователей SAS из Azure PowerShell учетная запись Azure AD, используемая для вхотливки в Azure CLI, должна быть назначена роль, включавв в себя действия **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Это разрешение позволяет учетной записи Azure AD запросить *ключ делегации пользователя.* Ключ делегирования пользователя используется для подписания делегации пользователя SAS. Роль, обеспечивающая действие **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey,** должно быть отнесено на уровне учетной записи хранилища, группы ресурсов или подписки.

Если у вас нет достаточных разрешений на присвоение ролей RBAC директору безопасности Azure AD, может потребоваться попросить владельца учетной записи или администратора назначить необходимые разрешения.

Следующий пример присваивает роль **вкладчика хранилища данных Blob,** которая включает в себя **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** действий. Роль приравнивается на уровне учетной записи хранилища.

Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Для получения дополнительной информации о встроенных ролях, включающие действия **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey,** [см.](../../role-based-access-control/built-in-roles.md)

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Используйте учетные данные Azure AD для обеспечения безопасности SAS

При создании делегации пользователей SAS с помощью Azure CLI ключ делегации пользователей, используемый для подписи SAS, создается для вас неявно. Время начала и время истечения времени, указанное для SAS, также используется в качестве времени начала и истечения времени для ключа делегации пользователя.

Поскольку максимальный интервал, в течение которого действует ключ делегации пользователя, составляет 7 дней с даты начала, следует указать время истечения срока действия SAS, которое составляет 7 дней после начала. SAS является недействительным после истечения срока действия ключа делегации пользователя, поэтому SAS со сроком действия более 7 дней будет по-прежнему действителен только в течение 7 дней.

При создании делегации `--auth-mode login` пользователя SAS, и `--as-user parameters` требуются. Укажите *логин* для `--auth-mode` параметра, чтобы запросы, сделанные в Хранилище Azure, были авторизованы с помощью учетных данных Azure AD. Указать `--as-user` параметр, указывающий на то, что возвращенным SAS должна быть делегация пользователя SAS.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Создание sAS-делегации пользователя для контейнера

Чтобы создать sAS-группу для контейнера с Azure CLI, позвоните в команду генерации [хранилища az-sas.](/cli/azure/storage/container#az-storage-container-generate-sas)

Поддерживаемые разрешения для sAS-группы пользователя включают добавление, создание, удаление, список, чтение и запись. Разрешения могут быть указаны по-пословам или комбинированным. Для получения дополнительной информации об этих разрешениях [см.](/rest/api/storageservices/create-user-delegation-sas)

Следующий пример возвращает токен SAS делегации пользователя sAS для контейнера. Не забудьте заменить значения заполнителя в скобках на свои собственные значения:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

Возврат токенов SAS будет аналогичен:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Создание делегации пользователя SAS для капли

Чтобы создать делегацию пользователя SAS для капли с Azure CLI, позвоните в команду [генерации капли хранения az.sas.](/cli/azure/storage/blob#az-storage-blob-generate-sas)

Поддерживаемые разрешения для делегации пользователя SAS на blob включают добавление, создание, удаление, чтение и запись. Разрешения могут быть указаны по-пословам или комбинированным. Для получения дополнительной информации об этих разрешениях [см.](/rest/api/storageservices/create-user-delegation-sas)

Следующий синтаксис возвращает делегацию пользователя SAS за каплей. Пример определяет `--full-uri` параметр, который возвращает blob URI с приложеным токеном SAS. Не забудьте заменить значения заполнителя в скобках на свои собственные значения:

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

Делегация пользователя SAS URI вернулась будет аналогична:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> Делегация пользователя SAS не поддерживает определение разрешений с политикой сохраненного доступа.

## <a name="revoke-a-user-delegation-sas"></a>Отозвать делегацию пользователей SAS

Чтобы отозвать sAS-группу пользователя из Azure CLI, позвоните в команду [az storage-s, отменяющий делегирование ключей.](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) Эта команда аннулирует все ключи делегации пользователя, связанные с указанной учетной записью хранения. Любые подписи общего доступа, связанные с этими ключами, аннулируются.

Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Как ключевые ключевые функции делегации пользователя, так и назначения ролей RBAC кэшируются Хранилищем Azure, поэтому может быть задержка между тем, когда вы инициируете процесс отзыва, и когда существующая делегация пользователя SAS становится недействительной.

## <a name="next-steps"></a>Дальнейшие действия

- [Создание делегации пользователей SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Получите операцию ключ к делегированию пользователя](/rest/api/storageservices/get-user-delegation-key)
