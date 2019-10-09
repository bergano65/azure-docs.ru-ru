---
title: Управление ключами учетной записи хранения с помощью Azure Key Vault и Azure CLI
description: Ключи учетной записи хранения обеспечивают тесную интеграцию между Azure Key Vault и доступом на основе ключей к учетной записи хранения Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 62faf33dc8b3690036407972e12633e741a85d78
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176749"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Управление ключами учетной записи хранения с помощью Key Vault и Azure CLI

Учетная запись хранения Azure использует учетные данные, состоящие из имени учетной записи и ключа. Ключ создается автоматически и используется как пароль, а не как криптографический ключ. Key Vault управляет ключами учетной записи хранения, сохраняя их в виде [Key Vault секретов](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

Вы можете использовать функцию ключа управляемой учетной записи хранения Key Vault, чтобы перечислить ключи (синхронизировать) с помощью учетной записи хранения Azure, а также периодически создавать их (циклически переключать). Вы можете управлять ключами как для учетных записей хранения, так и для классических учетных записей хранения.

При использовании функции ключа управляемой учетной записи хранения учитывайте следующие моменты.

- Значения ключа никогда не возвращаются в ответ на вызывающий объект.
- Ключи учетной записи хранения должны управлять только Key Vault. Не управляйте ключами самостоятельно и Избегайте конфликтов с Key Vault процессами.
- Только один объект Key Vault должен управлять ключами учетной записи хранения. Не разрешать управление ключами из нескольких объектов.
- Вы можете запросить Key Vault управления учетной записью хранения с помощью субъекта-пользователя, но не с субъектом-службой.
- Повторное создание ключей с помощью только Key Vault. Не пересоздавайте ключи учетной записи хранения вручную.

Мы рекомендуем использовать интеграцию службы хранилища Azure с Azure Active Directory (Azure AD), облачной службой управления удостоверениями и доступом Майкрософт. Интеграция Azure AD доступна для [больших двоичных объектов и очередей Azure](../storage/common/storage-auth-aad.md), а также предоставляет доступ на основе маркеров OAuth2 к службе хранилища Azure (как Azure Key Vault).

Azure AD позволяет проверять подлинность клиентского приложения с помощью удостоверения приложения или пользователя, а не учетных данных учетной записи хранения. Вы можете использовать [управляемое удостоверение Azure AD](/azure/active-directory/managed-identities-azure-resources/) при запуске в Azure. Управляемые удостоверения изменяют необходимость проверки подлинности клиента и сохраняют учетные данные в приложении или вместе с ним.

Azure AD использует управление доступом на основе ролей (RBAC) для управления авторизацией, которая также поддерживается Key Vault.

## <a name="service-principal-application-id"></a>Идентификатор приложения субъекта-службы

Клиент Azure AD предоставляет каждому зарегистрированному приложению субъект- [службу](/azure/active-directory/develop/developer-glossary#service-principal-object). Субъект-служба выступает в качестве идентификатора приложения, который используется во время настройки авторизации для доступа к другим ресурсам Azure через RBAC.

Key Vault — это приложение Майкрософт, которое предварительно зарегистрировано во всех клиентах Azure AD. Key Vault регистрируется в одном и том же ИДЕНТИФИКАТОРе приложения в каждом облаке Azure.

| Клиенты | Облако | Идентификатор приложения |
| --- | --- | --- |
| Azure AD | Azure для государственных организаций | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Общедоступная служба Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Другие  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством необходимо сначала выполнить следующие действия.

- [Установка Azure CLI](/cli/azure/install-azure-cli).
- [Создайте хранилище ключей.](quick-create-cli.md)
- [Создайте учетную запись хранения Azure](../storage/common/storage-quickstart-create-account.md?tabs=azure-cli). Имя учетной записи хранения должно содержать только строчные буквы и цифры. Длина имени должна составлять от 3 до 24 символов.
      
## <a name="manage-storage-account-keys"></a>Управление ключами учетной записи хранения

### <a name="connect-to-your-azure-account"></a>Подключение к учетной записи Azure

Проверьте подлинность сеанса Azure CLI с помощью команд [AZ login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Предоставление Key Vault доступа к учетной записи хранения

Воспользуйтесь командой Azure CLI [AZ Role назначение Create](/cli/azure/role/assignment?view=azure-cli-latest) , чтобы предоставить Key Vault доступ к вашей учетной записи хранения. Укажите следующие значения параметров для команды:

- `--role`. Передайте роль службы оператора ключа "оператор ключей учетной записи хранения". Эта роль ограничивает область доступа учетной записью хранения. Для классической учетной записи хранения передайте "роль службы оператора ключа классической учетной записи хранения".
- `--assignee-object-id`. Передайте значение "93c27d83-f79b-4cb2-8dd4-4aa716542e74", которое является ИДЕНТИФИКАТОРом объекта для Key Vault в общедоступном облаке Azure. (Чтобы получить идентификатор объекта для Key Vault в облаке Azure для государственных организаций, см. раздел [идентификатор приложения субъекта-службы](#service-principal-application-id).)
- `--scope`. Передайте идентификатор ресурса учетной записи хранения, который имеет форму `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`. Чтобы найти идентификатор подписки, воспользуйтесь командой Azure CLI [AZ Account List](/cli/azure/account?view=azure-cli-latest#az-account-list) . чтобы найти имя учетной записи хранения и группу ресурсов учетной записи хранения, воспользуйтесь командой Azure CLI [AZ Storage Account List](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) .

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>Создание управляемой учетной записи хранения Key Vault

 Создайте Key Vault управляемую учетную запись хранения с помощью команды Azure CLI [AZ keyvault Storage](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) . Задайте период повторного создания 90 дней. Через 90 дней Key Vault повторно создает `key1` и меняет местами активный ключ с `key2` на `key1`. `key1` помечается как активный ключ. Укажите следующие значения параметров для команды:

- `--vault-name`. Передайте имя хранилища ключей. Чтобы найти имя хранилища ключей, воспользуйтесь командой Azure CLI [AZ keyvault List](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) .
- `-n`. Передайте имя учетной записи хранения. Чтобы найти имя учетной записи хранения, используйте команду [AZ Storage Account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) Azure CLI.
- `--resource-id`. Передайте идентификатор ресурса учетной записи хранения, который имеет форму `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`. Чтобы найти идентификатор подписки, воспользуйтесь командой Azure CLI [AZ Account List](/cli/azure/account?view=azure-cli-latest#az-account-list) . чтобы найти имя учетной записи хранения и группу ресурсов учетной записи хранения, воспользуйтесь командой Azure CLI [AZ Storage Account List](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) .
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Токены подписи общего доступа

Вы также можете попросить Key Vault создать маркеры подписи общего доступа. Подпись общего доступа обеспечивает делегированный доступ к ресурсам в вашей учетной записи хранения. Вы можете предоставить клиентам доступ к ресурсам в вашей учетной записи хранения без предоставления общего доступа к ключам учетной записи. Подписанный URL-доступ обеспечивает безопасный способ предоставления общего доступа к ресурсам хранилища без ущерба для ключей учетной записи.

Команды в этом разделе выполняются в следующих действиях.

- Задайте для учетной записи определение подписи общего доступа `<YourSASDefinitionName>`. Определение задается в Key Vault управляемой учетной записи хранения `<YourStorageAccountName>` в хранилище ключей `<YourKeyVaultName>`.
- Создание маркера подписанного URL-имени учетной записи для служб BLOB-объектов, файлов, таблиц и очередей. Маркер создается для служб типов ресурсов, контейнеров и объектов. Маркер создается со всеми разрешениями, по протоколу HTTPS и с указанными датами начала и окончания.
- Задайте определение подписанного общего доступа к хранилищу Key Vault в хранилище. Определение содержит универсальный код ресурса (URI) шаблона созданного маркера подписи общего доступа. Определение имеет тип подписи общего доступа `account` и является допустимым в течение N дней.
- Убедитесь, что подписанный URL-доступ сохранен в хранилище ключей в качестве секрета.

### <a name="create-a-shared-access-signature-token"></a>Создание маркера подписи общего доступа

Создайте определение подписи общего доступа с помощью команды Azure CLI [AZ Storage Account Generate-SAS](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) . Для этой операции требуются разрешения `storage` и `setsas`.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
После успешного выполнения операции скопируйте выходные данные.

```console
"se=2020-01-01&sp=***"
```

Эти выходные данные будут переданы параметру `--template-id` на следующем шаге.

### <a name="generate-a-shared-access-signature-definition"></a>Создание определения подписи общего доступа

Используйте команду Azure CLI [AZ keyvault Storage SAS-Definition Create](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) , передав выходные данные предыдущего шага в параметр `--template-id`, чтобы создать определение подписи общего доступа.  Вы можете указать имя своего выбора в параметре `-n`.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Проверка определения подписи общего доступа

Вы можете проверить, что определение подписанного URL в хранилище ключей сохранено, с помощью Azure CLI [AZ keyvault Secret List](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) и [AZ keyvault Secret показывать](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) команды.

Сначала найдите определение подписи общего доступа в хранилище ключей, выполнив команду [AZ keyvault Secret List](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) .

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

Секрет, соответствующий определению SAS, будет иметь следующие свойства:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

Теперь можно использовать команду [AZ keyvault Secret Показать](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) и свойство `id` для просмотра содержимого этого секрета.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

Выходные данные этой команды будут показывать строку определения SAS в виде @ no__t-0.


## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о [ключах, секретах и сертификатах](https://docs.microsoft.com/rest/api/keyvault/).
- Ознакомьтесь с статьями в [блоге Azure Key Vault Team](https://blogs.technet.microsoft.com/kv/).
- См. справочную документацию по [AZ keyvault Storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) .
