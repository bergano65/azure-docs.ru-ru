---
title: Управление службой Блокчейн Azure с помощью Azure CLI
description: Создание службы Блокчейн Azure и управление ею с помощью Azure CLI
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 4dd58f2542674633f2d5e2a1724adc7934d7f030
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307044"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Управление службой Блокчейн Azure с помощью Azure CLI

В дополнение к портал Azure можно использовать Azure CLI для управления членами блокчейн и узлами транзакций службы Блокчейн Azure.

Убедитесь, что вы установили последнюю [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) и выполнили вход в учетную запись Azure в `az login`с помощью.

В следующих примерах замените пример `<parameter names>` собственными значениями.

## <a name="create-blockchain-member"></a>Создание элемента блокчейн

Пример создает элемент блокчейн в службе Блокчейн Azure, который выполняет протокол кворума в новом консорциуме.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| Параметр | Описание |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой создаются ресурсы службы "Блокчейн Azure". |
| **name** | Уникальное имя, идентифицирующее участника блокчейн-сети в службе "Блокчейн Azure". Это имя используется в адресе общедоступной конечной точки. Например, `myblockchainmember.blockchain.azure.com`. |
| **расположение** | Регион Azure, в котором создается участник блокчейн-сети. Например, `eastus`. Выберите расположение, наиболее близкое к пользователям или другим приложениям Azure. |
| **password** | Пароль учетной записи участника. Пароль учетной записи участника используется для обычной аутентификации в общедоступной конечной точке участника блокчейн-сети. Пароль должен соответствовать трем из следующих четырех требований: длина должна составлять от 12 & 72 символов, содержать от 1 строчной буквы, 1 прописную букву, 1 число и один специальный символ, не являющийся знаком (#), percent (%), запятая (,), звездочка (*), Обратная кавычка (\`), двойная кавычка ("), одинарная кавычка ('), тире (-) и семиколумн (;)|
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
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| Параметр | Описание |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой создаются ресурсы службы "Блокчейн Azure". |
| **name** | Имя, идентифицирующее участника службы Azure Блокчейн. |
| **password** | Пароль учетной записи участника. Пароль должен соответствовать трем из следующих четырех требований: длина должна составлять от 12 & 72 символов, содержать от 1 строчной буквы, 1 прописную букву, 1 число и один специальный символ, не являющийся знаком (#), percent (%), запятая (,), звездочка (*), Обратная кавычка (\`), двойная кавычка ("), одинарная кавычка ('), тире (-) и точка с запятой (;). |

## <a name="create-transaction-node"></a>Создание узла транзакций

Создайте узел транзакции внутри существующего элемента блокчейн. Добавляя узлы транзакций, можно повысить изоляцию безопасности и распределить нагрузку. Например, у вас может быть конечная точка узла транзакции для разных клиентских приложений.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| Параметр | Описание |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой создаются ресурсы службы "Блокчейн Azure". |
| **name** | Имя участника Azure Блокчейн Service блокчейн, который также включает новое имя узла транзакции. |
| **расположение** | Регион Azure, в котором создается участник блокчейн-сети. Например, `eastus`. Выберите расположение, наиболее близкое к пользователям или другим приложениям Azure. |
| **password** | Пароль узла транзакции. Пароль должен соответствовать трем из следующих четырех требований: длина должна составлять от 12 & 72 символов, содержать от 1 строчной буквы, 1 прописную букву, 1 число и один специальный символ, не являющийся знаком (#), percent (%), запятая (,), звездочка (*), Обратная кавычка (\`), двойная кавычка ("), одинарная кавычка ('), тире (-) и точка с запятой (;). |
| **ruleName** | Имя правила для список разрешений диапазон IP-адресов. Необязательный параметр для правил брандмауэра. |
| **Параметров startipaddress** | Начало диапазона IP-адресов для список разрешений. Необязательный параметр для правил брандмауэра. |
| **endIpAddress** | Конец диапазона IP-адресов для список разрешений. Необязательный параметр для правил брандмауэра.|

## <a name="change-transaction-node-password"></a>Изменение пароля узла транзакции

Пример изменяет пароль узла транзакции.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| Параметр | Описание |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой существуют ресурсы службы Блокчейн Azure. |
| **name** | Имя участника Azure Блокчейн Service блокчейн, который также включает новое имя узла транзакции. |
| **password** | Пароль узла транзакции. Пароль должен соответствовать трем из следующих четырех требований: длина должна составлять от 12 & 72 символов, содержать от 1 строчной буквы, 1 прописную букву, 1 число и один специальный символ, не являющийся знаком (#), percent (%), запятая (,), звездочка (*), Обратная кавычка (\`), двойная кавычка ("), одинарная кавычка ('), тире (-) и точка с запятой (;). |

## <a name="change-consortium-management-account-password"></a>Изменение пароля учетной записи управления консорциумом

Учетная запись управления консорциумом используется для управления членством в консорциуме W3C. Каждый элемент однозначно идентифицируется учетной записью управления консорциумом, и вы можете изменить пароль этой учетной записи с помощью следующей команды.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| Параметр | Описание |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой создаются ресурсы службы "Блокчейн Azure". |
| **name** | Имя, идентифицирующее участника службы Azure Блокчейн. |
| **consortiumManagementAccountPassword** | Пароль учетной записи управления консорциумом. Пароль должен соответствовать трем из следующих четырех требований: длина должна составлять от 12 & 72 символов, содержать от 1 строчной буквы, 1 прописную букву, 1 число и один специальный символ, не являющийся знаком (#), percent (%), запятая (,), звездочка (*), Обратная кавычка (\`), двойная кавычка ("), одинарная кавычка ('), тире (-) и точка с запятой (;). |
  
## <a name="update-firewall-rules"></a>Обновление правил брандмауэра

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| Параметр | Описание |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой существуют ресурсы службы Блокчейн Azure. |
| **name** | Имя члена блокчейн службы Azure Блокчейн. |
| **ruleName** | Имя правила для список разрешений диапазон IP-адресов. Необязательный параметр для правил брандмауэра.|
| **Параметров startipaddress** | Начало диапазона IP-адресов для список разрешений. Необязательный параметр для правил брандмауэра.|
| **endIpAddress** | Конец диапазона IP-адресов для список разрешений. Необязательный параметр для правил брандмауэра.|

## <a name="list-api-keys"></a>Список ключей API

Ключи API можно использовать для доступа к узлам аналогично имени пользователя и паролю. Существует два ключа API для поддержки смены ключей. Используйте следующую команду, чтобы получить список ключей API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| Параметр | Описание |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой существуют ресурсы службы Блокчейн Azure. |
| **name** | Имя участника Azure Блокчейн Service блокчейн, который также включает новое имя узла транзакции. |

## <a name="regenerate-api-keys"></a>Повторное создание ключей API

Используйте следующую команду, чтобы повторно создать ключи API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| Параметр | Описание |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой существуют ресурсы службы Блокчейн Azure. |
| **name** | Имя участника Azure Блокчейн Service блокчейн, который также включает новое имя узла транзакции. |
| **keyName** | Замените \<KeyValue\> на key1 или Key2. |

## <a name="delete-a-transaction-node"></a>Удаление узла транзакции

Пример удаляет узел транзакции-члена блокчейн.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Параметр | Описание |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой существуют ресурсы службы Блокчейн Azure. |
| **name** | Имя члена Azure Блокчейн Service блокчейн, который также включает имя нового узла транзакции, который необходимо удалить. |

## <a name="delete-a-blockchain-member"></a>Удаление элемента блокчейн

Пример удаляет элемент блокчейн.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Параметр | Описание |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой существуют ресурсы службы Блокчейн Azure. |
| **name** | Имя удаляемого члена Azure Блокчейн Service блокчейн. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Предоставление доступа для пользователя Azure AD

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Параметр | Описание |
|---------|-------------|
| **role** | Имя роли Azure AD. |
| **уполномоченного** | Идентификатор пользователя Azure AD. Например: `user@contoso.com` |
| **scope** | Область назначения роли. Может быть либо блокчейн элементом, либо узлом транзакции. |

**Пример.**

Предоставьте доступ к узлу для пользователя Azure AD **члену**блокчейн:

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Пример.**

Предоставьте доступ к узлу для пользователя Azure AD на **узел транзакции**блокчейн:

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Предоставление доступа к узлу для роли приложения или группы Azure AD

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| Параметр | Описание |
|---------|-------------|
| **role** | Имя роли Azure AD. |
| **assigns-Object-ID** | Идентификатор группы Azure AD или идентификатор приложения. |
| **scope** | Область назначения роли. Может быть либо блокчейн элементом, либо узлом транзакции. |

**Пример.**

Предоставление доступа к узлу для **роли приложения**

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Удаление доступа к узлу Azure AD

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Параметр | Описание |
|---------|-------------|
| **role** | Имя роли Azure AD. |
| **уполномоченного** | Идентификатор пользователя Azure AD. Например: `user@contoso.com` |
| **scope** | Область назначения роли. Может быть либо блокчейн элементом, либо узлом транзакции. |

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Настройка узлов транзакций службы Azure Блокчейн с портал Azure](configure-transaction-nodes.md)
