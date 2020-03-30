---
title: Управление блокчейн-сервисом Azure с помощью Azure CLI
description: Как управлять блокчейн-сервисом Azure с Azure CLI
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455583"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Управление блокчейн-сервисом Azure с помощью Azure CLI

Помимо портала Azure, вы можете использовать Azure CLI для управления участниками блокчейна и узлов транзакций для службы блокчейн Azure.

Убедитесь, что вы установили последнюю [учетную запись Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) и `az login`вошли в учетную запись Azure с помощью.

В следующих примерах `<parameter names>` замените пример своими ценностями.

## <a name="create-blockchain-member"></a>Создание участника блокчейна

Пример создает участника блокчейна в Azure Blockchain Service, который запускает протокол кворума в новом консорциуме.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| Параметр | Описание |
|---------|-------------|
| **ресурсная группа** | Имя группы ресурсов, в которой создаются ресурсы службы "Блокчейн Azure". |
| **name** | Уникальное имя, идентифицирующее участника блокчейн-сети в службе "Блокчейн Azure". Это имя используется в адресе общедоступной конечной точки. Например, `myblockchainmember.blockchain.azure.com`. |
| **Расположение** | Регион Azure, в котором создается участник блокчейн-сети. Например, `eastus`. Выберите расположение, наиболее близкое к пользователям или другим приложениям Azure. |
| **пароль** | Пароль учетной записи участника. Пароль учетной записи участника используется для обычной аутентификации в общедоступной конечной точке участника блокчейн-сети. Пароль должен соответствовать трем из следующих четырех требований: длина должна быть между 12 & 72 символов, 1 нижний символ случае, 1 верхний символ корпуса, 1 число, и 1 специальный символ, который не номер знака (я), процент (%), запятая (,), звезда () назад цитата (\`), двойная цитата ('), тире (-) и полуколонна (;)|
| **Протокол** | Публичный предварительный просмотр поддерживает Кворум. |
| **Консорциум** | Имя консорциума, который создается или к которому присоединяется участник. |
| **consortiumManagementAccountPassword** | Пароль управления консорциума. Пароль используется для присоединения к консорциуму. |
| **ruleName** | Название правила для белого списка диапазона IP-адресов. Дополнительный параметр для правил брандмауэра.|
| **startIpАдрес** | Запуск диапазона IP-адресов для белого списка. Дополнительный параметр для правил брандмауэра. |
| **endipAddress** | Конец диапазона IP-адресов для белого списка. Дополнительный параметр для правил брандмауэра. |
| **skuName** | Тип уровня. Используйте S0, чтобы выбрать ценовую категорию "Стандартный", или B0, чтобы выбрать ценовую категорию "Базовый". |

## <a name="change-blockchain-member-password"></a>Изменение пароля участника блокчейна

Пример изменяет пароль участника блокчейна.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Параметр | Описание |
|---------|-------------|
| **ресурсная группа** | Имя группы ресурсов, в которой создаются ресурсы службы "Блокчейн Azure". |
| **name** | Имя, идентифицирущее пользователя службы блокчейн Azure. |
| **пароль** | Пароль учетной записи участника. Пароль должен соответствовать трем из следующих четырех требований: длина должна быть между 12 & 72 символов, 1 нижний символ случае, 1 верхний символ корпуса, 1 число, и 1 специальный символ, который не номер знака (я), процент (%), запятая (,), звезда (), назад цитатой(\`), двойная цитата ('), одиночный характер ('), тире (-) и запятая (;). |

## <a name="create-transaction-node"></a>Создание узла транзакций

Создайте узла транзакции внутри существующего элемента блокчейна. Добавляя узлы транзакций, можно увеличить изоляцию безопасности и распределить нагрузку. Например, можно иметь конечную точку узла транзакций для различных клиентских приложений.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| Параметр | Описание |
|---------|-------------|
| **ресурсная группа** | Имя группы ресурсов, в которой создаются ресурсы службы "Блокчейн Azure". |
| **name** | Имя участника блокчейн-сервиса Azure Blockchain Service, который также включает новое имя узла транзакции. |
| **Расположение** | Регион Azure, в котором создается участник блокчейн-сети. Например, `eastus`. Выберите расположение, наиболее близкое к пользователям или другим приложениям Azure. |
| **пароль** | Пароль узла транзакции. Пароль должен соответствовать трем из следующих четырех требований: длина должна быть между 12 & 72 символов, 1 нижний символ случае, 1 верхний символ корпуса, 1 число, и 1 специальный символ, который не номер знака (я), процент (%), запятая (,), звезда (), назад цитатой(\`), двойная цитата ('), одиночный характер ('), тире (-) и запятая (;). |
| **ruleName** | Название правила для белого списка диапазона IP-адресов. Дополнительный параметр для правил брандмауэра. |
| **startIpАдрес** | Запуск диапазона IP-адресов для белого списка. Дополнительный параметр для правил брандмауэра. |
| **endipAddress** | Конец диапазона IP-адресов для белого списка. Дополнительный параметр для правил брандмауэра.|

## <a name="change-transaction-node-password"></a>Изменение пароля узла транзакции

Пример изменяет пароль узла транзакции.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| Параметр | Описание |
|---------|-------------|
| **ресурсная группа** | Название группы ресурсов, где существуют ресурсы Azure Blockchain Service. |
| **name** | Имя участника блокчейн-сервиса Azure Blockchain Service, который также включает новое имя узла транзакции. |
| **пароль** | Пароль узла транзакции. Пароль должен соответствовать трем из следующих четырех требований: длина должна быть между 12 & 72 символов, 1 нижний символ случае, 1 верхний символ корпуса, 1 число, и 1 специальный символ, который не номер знака (я), процент (%), запятая (,), звезда (), назад цитатой(\`), двойная цитата ('), одиночный характер ('), тире (-) и запятая (;). |

## <a name="change-consortium-management-account-password"></a>Изменение пароля учетной записи управления консорциумом

Счет управления консорциумом используется для управления членством в консорциуме. Каждый участник однозначно идентифицирован учетной записью управления консорциумом, и вы можете изменить пароль этой учетной записи со следующей командой.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Параметр | Описание |
|---------|-------------|
| **ресурсная группа** | Имя группы ресурсов, в которой создаются ресурсы службы "Блокчейн Azure". |
| **name** | Имя, идентифицирущее пользователя службы блокчейн Azure. |
| **consortiumManagementAccountPassword** | Пароль учетной записи управления консорциумом. Пароль должен соответствовать трем из следующих четырех требований: длина должна быть между 12 & 72 символов, 1 нижний символ случае, 1 верхний символ корпуса, 1 число, и 1 специальный символ, который не номер знака (я), процент (%), запятая (,), звезда (), назад цитатой(\`), двойная цитата ('), одиночный характер ('), тире (-) и запятая (;). |
  
## <a name="update-firewall-rules"></a>Обновление правил брандмауэра

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| Параметр | Описание |
|---------|-------------|
| **ресурсная группа** | Название группы ресурсов, где существуют ресурсы Azure Blockchain Service. |
| **name** | Имя участника блокчейн-сервиса Azure Blockchain Service. |
| **ruleName** | Название правила для белого списка диапазона IP-адресов. Дополнительный параметр для правил брандмауэра.|
| **startIpАдрес** | Запуск диапазона IP-адресов для белого списка. Дополнительный параметр для правил брандмауэра.|
| **endipAddress** | Конец диапазона IP-адресов для белого списка. Дополнительный параметр для правил брандмауэра.|

## <a name="list-api-keys"></a>Список aPI-ключей

Ключи API могут быть использованы для доступа к узлам, аналогичному имени пользователя и паролю. Есть два ключа API для поддержки вращения ключей. Используйте следующую команду, чтобы перечислить клавиши API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Параметр | Описание |
|---------|-------------|
| **ресурсная группа** | Название группы ресурсов, где существуют ресурсы Azure Blockchain Service. |
| **name** | Имя участника блокчейн-сервиса Azure Blockchain Service, который также включает новое имя узла транзакции. |

## <a name="regenerate-api-keys"></a>Регенерация ключей API

Используйте следующую команду для регенерации ключей API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| Параметр | Описание |
|---------|-------------|
| **ресурсная группа** | Название группы ресурсов, где существуют ресурсы Azure Blockchain Service. |
| **name** | Имя участника блокчейн-сервиса Azure Blockchain Service, который также включает новое имя узла транзакции. |
| **keyName** | Замените \<\> keyValue ключом 1 или ключом2. |

## <a name="delete-a-transaction-node"></a>Удаление узла транзакции

Пример удаляет узла транзакции участника блокчейна.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Параметр | Описание |
|---------|-------------|
| **ресурсная группа** | Название группы ресурсов, где существуют ресурсы Azure Blockchain Service. |
| **name** | Имя участника блокчейн-сервиса Azure Blockchain Service, который также включает имя узла транзакции, которое будет удалено. |

## <a name="delete-a-blockchain-member"></a>Удалить участника блокчейна

Пример удаляет участника блокчейна.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Параметр | Описание |
|---------|-------------|
| **ресурсная группа** | Название группы ресурсов, где существуют ресурсы Azure Blockchain Service. |
| **name** | Имя участника блокчейн-сервиса Azure Blockchain Service будет удалено. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Грант доступ для пользователя Azure AD

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Параметр | Описание |
|---------|-------------|
| **Роль** | Название роли Azure AD. |
| **assignee** | Идентификатор пользователя Azure AD. Например `user@contoso.com`. |
| **область действия** | Область назначения ролей. Может быть либо членом блокчейна, либо узлатранзакций. |

**Примере:**

Грант узел доступа для пользователя Azure AD к **члену**blockchain:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Примере:**

Грант узел доступа для пользователя Azure AD к **блокчейн-узла транзакций:**

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Доступ к узелу Гранта для группы Azure AD или роли приложения

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Параметр | Описание |
|---------|-------------|
| **Роль** | Название роли Azure AD. |
| **назначить-объект-id** | Идентификатор группы Azure AD или идентификатор приложения. |
| **область действия** | Область назначения ролей. Может быть либо членом блокчейна, либо узлатранзакций. |

**Примере:**

Доступ к узлам Гранта для **роли приложения**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Удалить доступ к узлову AD Azure

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Параметр | Описание |
|---------|-------------|
| **Роль** | Название роли Azure AD. |
| **assignee** | Идентификатор пользователя Azure AD. Например `user@contoso.com`. |
| **область действия** | Область назначения ролей. Может быть либо членом блокчейна, либо узлатранзакций. |

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как [настроить узлы транзакций Azure Blockchain Service с помощью портала Azure.](configure-transaction-nodes.md)
