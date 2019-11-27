---
title: Управление службой Блокчейн Azure с помощью Azure CLI
description: Управление службой Блокчейн Azure с помощью Azure CLI
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455583"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Управление службой Блокчейн Azure с помощью Azure CLI

В дополнение к портал Azure можно использовать Azure CLI для управления членами блокчейн и узлами транзакций службы Блокчейн Azure.

Убедитесь, что вы установили последнюю [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) и выполнили вход в учетную запись Azure в с помощью `az login`.

В следующих примерах замените пример `<parameter names>` собственными значениями.

## <a name="create-blockchain-member"></a>Создание элемента блокчейн

Пример создает элемент блокчейн в службе Блокчейн Azure, который выполняет протокол кворума в новом консорциуме.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой создаются ресурсы службы "Блокчейн Azure". |
| **name** | Уникальное имя, идентифицирующее участника блокчейн-сети в службе "Блокчейн Azure". Это имя используется в адресе общедоступной конечной точки. Пример: `myblockchainmember.blockchain.azure.com`. |
| **location** | Регион Azure, в котором создается участник блокчейн-сети. Пример: `eastus`. Выберите расположение, наиболее близкое к пользователям или другим приложениям Azure. |
| **password** | Пароль учетной записи участника. Пароль учетной записи участника используется для обычной аутентификации в общедоступной конечной точке участника блокчейн-сети. Пароль должен соответствовать трем из следующих четырех требований: длина должна составлять от 12 & 72 символов, строчных символов, прописных букв, 1 числа и специальных символов, которые не являются решетками (#), процентов (%), запятых (,), звездочки (*), обратной кавычки (\`), двойных кавычек ("), одинарной кавычки ('), тире (-) и семиколумн (;)|
| **protocol** | Общедоступная Предварительная версия поддерживает кворум. |
| **consortium** | Имя консорциума, который создается или к которому присоединяется участник. |
| **consortiumManagementAccountPassword** | Пароль управления консорциума. Пароль используется для присоединения к консорциуму. |
| **ruleName** | Имя правила для список разрешений диапазон IP-адресов. Необязательный параметр для правил брандмауэра.|
| **Параметров startipaddress** | Начало диапазона IP-адресов для список разрешений. Необязательный параметр для правил брандмауэра. |
| **endIpAddress** | Конец диапазона IP-адресов для список разрешений. Необязательный параметр для правил брандмауэра. |
| **skuName** | Тип уровня. Используйте S0, чтобы выбрать ценовую категорию "Стандартный", или B0, чтобы выбрать ценовую категорию "Базовый". |

## <a name="change-blockchain-member-password"></a>Изменить пароль члена блокчейн

В примере изменяется пароль участника блокчейн.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой создаются ресурсы службы "Блокчейн Azure". |
| **name** | Имя, идентифицирующее участника службы Azure Блокчейн. |
| **password** | Пароль учетной записи участника. Пароль должен соответствовать трем из следующих четырех требований: длина должна составлять от 12 & 72 символов, строчных букв, прописных букв, 1 числа и специальных символов, не имеющих цифр (#), процентов (%), запятых (,), звездочки (*), обратной кавычки (\`), двойных кавычек ("), одинарных кавычек ('), тире (-) и точки с запятой (;). |

## <a name="create-transaction-node"></a>Создание узла транзакций

Создайте узел транзакции внутри существующего элемента блокчейн. Добавляя узлы транзакций, можно повысить изоляцию безопасности и распределить нагрузку. Например, у вас может быть конечная точка узла транзакции для разных клиентских приложений.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой создаются ресурсы службы "Блокчейн Azure". |
| **name** | Имя участника Azure Блокчейн Service блокчейн, который также включает новое имя узла транзакции. |
| **location** | Регион Azure, в котором создается участник блокчейн-сети. Пример: `eastus`. Выберите расположение, наиболее близкое к пользователям или другим приложениям Azure. |
| **password** | Пароль узла транзакции. Пароль должен соответствовать трем из следующих четырех требований: длина должна составлять от 12 & 72 символов, строчных букв, прописных букв, 1 числа и специальных символов, не имеющих цифр (#), процентов (%), запятых (,), звездочки (*), обратной кавычки (\`), двойных кавычек ("), одинарных кавычек ('), тире (-) и точки с запятой (;). |
| **ruleName** | Имя правила для список разрешений диапазон IP-адресов. Необязательный параметр для правил брандмауэра. |
| **Параметров startipaddress** | Начало диапазона IP-адресов для список разрешений. Необязательный параметр для правил брандмауэра. |
| **endIpAddress** | Конец диапазона IP-адресов для список разрешений. Необязательный параметр для правил брандмауэра.|

## <a name="change-transaction-node-password"></a>Изменение пароля узла транзакции

Пример изменяет пароль узла транзакции.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой существуют ресурсы службы Блокчейн Azure. |
| **name** | Имя участника Azure Блокчейн Service блокчейн, который также включает новое имя узла транзакции. |
| **password** | Пароль узла транзакции. Пароль должен соответствовать трем из следующих четырех требований: длина должна составлять от 12 & 72 символов, строчных букв, прописных букв, 1 числа и специальных символов, не имеющих цифр (#), процентов (%), запятых (,), звездочки (*), обратной кавычки (\`), двойных кавычек ("), одинарных кавычек ('), тире (-) и точки с запятой (;). |

## <a name="change-consortium-management-account-password"></a>Изменение пароля учетной записи управления консорциумом

Учетная запись управления консорциумом используется для управления членством в консорциуме W3C. Каждый элемент однозначно идентифицируется учетной записью управления консорциумом, и вы можете изменить пароль этой учетной записи с помощью следующей команды.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой создаются ресурсы службы "Блокчейн Azure". |
| **name** | Имя, идентифицирующее участника службы Azure Блокчейн. |
| **consortiumManagementAccountPassword** | Пароль учетной записи управления консорциумом. Пароль должен соответствовать трем из следующих четырех требований: длина должна составлять от 12 & 72 символов, строчных букв, прописных букв, 1 числа и специальных символов, не имеющих цифр (#), процентов (%), запятых (,), звездочки (*), обратной кавычки (\`), двойных кавычек ("), одинарных кавычек ('), тире (-) и точки с запятой (;). |
  
## <a name="update-firewall-rules"></a>Обновление правил брандмауэра

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой существуют ресурсы службы Блокчейн Azure. |
| **name** | Имя члена блокчейн службы Azure Блокчейн. |
| **ruleName** | Имя правила для список разрешений диапазон IP-адресов. Необязательный параметр для правил брандмауэра.|
| **Параметров startipaddress** | Начало диапазона IP-адресов для список разрешений. Необязательный параметр для правил брандмауэра.|
| **endIpAddress** | Конец диапазона IP-адресов для список разрешений. Необязательный параметр для правил брандмауэра.|

## <a name="list-api-keys"></a>Список ключей API

Ключи API можно использовать для доступа к узлам аналогично имени пользователя и паролю. Существует два ключа API для поддержки смены ключей. Используйте следующую команду, чтобы получить список ключей API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой существуют ресурсы службы Блокчейн Azure. |
| **name** | Имя участника Azure Блокчейн Service блокчейн, который также включает новое имя узла транзакции. |

## <a name="regenerate-api-keys"></a>Повторное создание ключей API

Используйте следующую команду, чтобы повторно создать ключи API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой существуют ресурсы службы Блокчейн Azure. |
| **name** | Имя участника Azure Блокчейн Service блокчейн, который также включает новое имя узла транзакции. |
| **keyName** | Замените \<keyValue\> с помощью key1 или Key2. |

## <a name="delete-a-transaction-node"></a>Удаление узла транзакции

Пример удаляет узел транзакции-члена блокчейн.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой существуют ресурсы службы Блокчейн Azure. |
| **name** | Имя члена блокчейн службы Azure Блокчейн, который также включает имя удаляемого узла транзакции. |

## <a name="delete-a-blockchain-member"></a>Удаление элемента блокчейн

Пример удаляет элемент блокчейн.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой существуют ресурсы службы Блокчейн Azure. |
| **name** | Имя удаляемого члена Azure Блокчейн Service блокчейн. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Предоставление доступа для пользователя Azure AD

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **role** | Имя роли Azure AD. |
| **уполномоченного** | Идентификатор пользователя Azure AD. Например, `user@contoso.com` |
| **scope** | Область назначения роли. Может быть либо блокчейн элементом, либо узлом транзакции. |

**Пример**

Предоставьте доступ к узлу для пользователя Azure AD **члену**блокчейн:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Пример**

Предоставьте доступ к узлу для пользователя Azure AD на **узел транзакции**блокчейн:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Предоставление доступа к узлу для роли приложения или группы Azure AD

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **role** | Имя роли Azure AD. |
| **assigns-Object-ID** | Идентификатор группы Azure AD или идентификатор приложения. |
| **scope** | Область назначения роли. Может быть либо блокчейн элементом, либо узлом транзакции. |

**Пример**

Предоставление доступа к узлу для **роли приложения**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Удаление доступа к узлу Azure AD

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **role** | Имя роли Azure AD. |
| **уполномоченного** | Идентификатор пользователя Azure AD. Например, `user@contoso.com` |
| **scope** | Область назначения роли. Может быть либо блокчейн элементом, либо узлом транзакции. |

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как [настроить узлы транзакций службы Блокчейн Azure с помощью портал Azure](configure-transaction-nodes.md).
