---
title: Управление службой Azure Blockchain, с помощью Azure CLI
description: Как создать и управлять службой Azure Блокчейн с помощью Azure CLI
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: d078ca181b2eed4b80d4f12f1c03b42f4e242194
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154439"
---
# <a name="manage-azure-blockchain-service-with-azure-cli"></a>Управление Azure Blockchain службой с помощью Azure CLI

Помимо портала Azure Azure CLI можно использовать для быстрого создания и управления блокчейн элементов и узлов транзакции для службы Azure Blockchain.

Убедитесь, что вы установили последнюю версию [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) и войдите в учетную запись Azure с помощью `az login`.

В следующих примерах Замените пример `<parameter names>` собственными значениями.

## <a name="create-blockchain-member"></a>Создание элемента блокчейна

Пример создает элемент блокчейна в службе Azure Blockchain, работающего в новый консорциума протокола книги кворума.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, где создаются ресурсы Azure Blockchain службы. |
| **name** | Уникальное имя, которое идентифицирует элемент блокчейн вашей службе Блокчейн в Azure. Имя используется для адреса общедоступной конечной точки. Например, `myblockchainmember.blockchain.azure.com`. |
| **расположение** | Регион Azure, где создается элемент блокчейна. Например, `eastus`. Выберите расположение, наиболее близкое к пользователям или другим приложениям Azure. |
| **password** | Пароль учетной записи члена. Пароль учетной записи члена используется для проверки подлинности к общедоступной конечной точке элемент блокчейн с обычной проверкой подлинности. Пароль должен соответствовать три из четырех следующих требований: длина должна находиться в диапазоне от 12 & 72 символов, одну строчную букву, одну прописную букву, одну цифру и один специальный символ, не число sign(#), percent(%), запятой (,), star(*), Обратная кавычка (\`), дважды quote("), одинарные, дефис и semicolumn(;)|
| **protocol** | Общедоступная Предварительная версия поддерживает кворума. |
| **Консорциум** | Имя консорциума присоединиться или создайте. |
| **consortiumManagementAccountPassword** | Пароль консорциума управления. Пароль, используемый для присоединения к консорциумом. |
| **Имя правила** | Имя правила для списка разрешенных диапазон IP-адресов. Необязательный параметр для правил брандмауэра.|
| **параметров startIpAddress** | Начало диапазона IP-адресов для списка разрешенных. Необязательный параметр для правил брандмауэра. |
| **endIpAddress** | Конец диапазона IP-адресов для списка разрешенных. Необязательный параметр для правил брандмауэра. |
| **SkuName** | Тип уровня. Используйте S0 Standard и B0 для Basic. |

## <a name="change-blockchain-member-password"></a>Изменение пароля член блокчейна

Пример изменяет пароль члена блокчейна.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, где создаются ресурсы Azure Blockchain службы. |
| **name** | Имя, которое идентифицирует элемент вашей службе Блокчейн в Azure. |
| **password** | Пароль учетной записи члена. Пароль должен соответствовать три из четырех следующих требований: длина должна находиться в диапазоне от 12 & 72 символов, одну строчную букву, одну прописную букву, одну цифру и один специальный символ, не число sign(#), percent(%), запятой (,), star(*), Обратная кавычка (\`), дважды quote("), одинарные, дефис и запятой (;). |


## <a name="create-transaction-node"></a>Создание узла транзакций

Создание узла транзакций внутри существующего члена блокчейна. Путем добавления узлов транзакции, вы сможете повысить изоляции и безопасности и распределения нагрузки. Например можно создать узел конечную точку транзакции для различных клиентских приложений.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, где создаются ресурсы Azure Blockchain службы. |
| **name** | Имя члена блокчейн Блокчейн службы Azure, который также включает в себя имя узла новой транзакции. |
| **расположение** | Регион Azure, где создается элемент блокчейна. Например, `eastus`. Выберите расположение, наиболее близкое к пользователям или другим приложениям Azure. |
| **password** | Пароль узла транзакции. Пароль должен соответствовать три из четырех следующих требований: длина должна находиться в диапазоне от 12 & 72 символов, одну строчную букву, одну прописную букву, одну цифру и один специальный символ, не число sign(#), percent(%), запятой (,), star(*), Обратная кавычка (\`), дважды quote("), одинарные, дефис и запятой (;). |
| **Имя правила** | Имя правила для списка разрешенных диапазон IP-адресов. Необязательный параметр для правил брандмауэра. |
| **параметров startIpAddress** | Начало диапазона IP-адресов для списка разрешенных. Необязательный параметр для правил брандмауэра. |
| **endIpAddress** | Конец диапазона IP-адресов для списка разрешенных. Необязательный параметр для правил брандмауэра.|

## <a name="change-transaction-node-password"></a>Изменение пароля узла транзакции

Пример изменяет пароль узла транзакции.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, где существуют ресурсы Azure Blockchain службы. |
| **name** | Имя члена блокчейн Блокчейн службы Azure, который также включает в себя имя узла новой транзакции. |
| **password** | Пароль узла транзакции. Пароль должен соответствовать три из четырех следующих требований: длина должна находиться в диапазоне от 12 & 72 символов, одну строчную букву, одну прописную букву, одну цифру и один специальный символ, не число sign(#), percent(%), запятой (,), star(*), Обратная кавычка (\`), дважды quote("), одинарные, дефис и запятой (;). |

## <a name="change-consortium-management-account-password"></a>Изменение пароля учетной записи управления консорциума

Учетная запись управления консорциума используется для управления членством консорциума. Каждый член однозначно идентифицируется с помощью учетной записи управления consortium, и можно изменить пароль этой учетной записи с помощью следующей команды.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, где создаются ресурсы Azure Blockchain службы. |
| **name** | Имя, которое идентифицирует элемент вашей службе Блокчейн в Azure. |
| **consortiumManagementAccountPassword** | Пароль учетной записи управления консорциума. Пароль должен соответствовать три из четырех следующих требований: длина должна находиться в диапазоне от 12 & 72 символов, одну строчную букву, одну прописную букву, одну цифру и один специальный символ, не число sign(#), percent(%), запятой (,), star(*), Обратная кавычка (\`), дважды quote("), одинарные, дефис и запятой (;). |
  
## <a name="update-firewall-rules"></a>Обновить правила брандмауэра

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, где существуют ресурсы Azure Blockchain службы. |
| **name** | Имя члена блокчейн Блокчейн службы Azure. |
| **Имя правила** | Имя правила для списка разрешенных диапазон IP-адресов. Необязательный параметр для правил брандмауэра.|
| **параметров startIpAddress** | Начало диапазона IP-адресов для списка разрешенных. Необязательный параметр для правил брандмауэра.|
| **endIpAddress** | Конец диапазона IP-адресов для списка разрешенных. Необязательный параметр для правил брандмауэра.|

## <a name="list-api-keys"></a>Список API ключей

Ключи API может использоваться для доступа узла, аналогичную имя пользователя и пароль. Существует два ключа API для поддержки смены ключей. Используйте следующую команду, чтобы получить список ключей API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, где существуют ресурсы Azure Blockchain службы. |
| **name** | Имя члена блокчейн Блокчейн службы Azure, который также включает в себя имя узла новой транзакции. |

## <a name="regenerate-api-keys"></a>Повторное создание ключей API

Повторное создание ключей API, используйте следующую команду.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, где существуют ресурсы Azure Blockchain службы. |
| **name** | Имя члена блокчейн Блокчейн службы Azure, который также включает в себя имя узла новой транзакции. |
| **keyName** | Замените \<keyValue\> key2 либо key1. |

## <a name="delete-a-transaction-node"></a>Удаление узла транзакций

Пример удаляет узел блокчейн члена транзакции.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, где существуют ресурсы Azure Blockchain службы. |
| **name** | Имя члена блокчейн Блокчейн службы Azure, который также включает в себя имя узла новой транзакции для удаления. |

## <a name="delete-a-blockchain-member"></a>Удаление элемента блокчейна

Пример удаляет член блокчейна.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, где существуют ресурсы Azure Blockchain службы. |
| **name** | Имя члена блокчейн Блокчейн службы Azure для удаления. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Предоставление доступа для пользователя Azure AD

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **Роли** | Имя роли Azure AD. |
| **уполномоченное лицо** | Идентификатор пользователя Azure AD. Например `user@contoso.com`. |
| **scope** | Область назначения роли. Может быть либо член блокчейн узла транзакций. |

**Пример.**

Предоставить доступ к узлам для пользователя Azure AD в блокчейн **член**:

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Пример.**

Предоставить доступ к узлам для пользователя Azure AD в блокчейн **узла транзакций**:

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Предоставить доступ к узлам для приложения или группы Azure AD роли

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **Роли** | Имя роли Azure AD. |
| **идентификатор для объекта уполномоченное лицо** | Идентификатор группы Azure AD или приложения идентификатор. |
| **scope** | Область назначения роли. Может быть либо член блокчейн узла транзакций. |

**Пример.**

Предоставить доступ к узлам для **роли приложения**

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Удалить доступ к узлам Azure AD

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **Роли** | Имя роли Azure AD. |
| **уполномоченное лицо** | Идентификатор пользователя Azure AD. Например `user@contoso.com`. |
| **scope** | Область назначения роли. Может быть либо член блокчейн узла транзакций. |

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Настройка службы Azure Blockchain транзакции узлов с помощью портала Azure](configure-transaction-nodes.md)
