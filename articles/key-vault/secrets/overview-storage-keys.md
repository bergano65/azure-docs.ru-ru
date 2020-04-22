---
title: Управление ключами учетной записи хранилища с помощью Azure Key Vault и Azure CLI
description: Ключи учетной записи хранилища обеспечивают беспрепятственную интеграцию между Azure Key Vault и ключевым доступом к учетной записи хранения Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 1125bafa43ce1752c58d1cce0bba66a6bbd32c32
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685419"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Управление ключами учетной записи хранилища с помощью Key Vault и Azure CLI

Учетная запись хранения Azure использует учетные данные, включающие имя учетной записи и ключ. Ключ автоматически генерируется и служит паролем, а не криптографическим ключом. Key Vault управляет ключами учетной записи хранилища, храним их как секреты Key Vault. 

Вы можете использовать ключевую функцию учетной записи с управлением хранилищем Key Vault для пересчета (синхронизации) ключей с учетной записью хранения Azure и периодического регенерации (поворота) ключей. Вы можете управлять ключами как для учетных записей хранения, так и для классических учетных записей хранения.

При использовании ключевой функции учетной записи управляемого хранилища следует учитывать следующие моменты:

- Ключевые значения никогда не возвращаются в ответ на абонента.
- Управлять ключами учетной записи хранилища должны только Key Vault. Не управляйте ключами самостоятельно и не вмешивайтесь в процессы Key Vault.
- Управлять ключами учетной записи хранилища должен только один объект Key Vault. Не разрешают управление ключами из нескольких объектов.
- Вы можете запросить Key Vault для управления учетной записью хранилища с помощью основного пользователя, но не с помощью принципала службы.
- Регенерировать ключи, используя только Key Vault. Не создавайте повторно ключи своей учетной записи хранения вручную.

Рекомендуется использовать интеграцию системы хранения данных Azure с Azure Active Directory (Azure AD), облачной службой идентификации и управления доступом корпорации Майкрософт. Интеграция Azure AD доступна для [капли и очередей Azure](../../storage/common/storage-auth-aad.md)и обеспечивает доступ к маркеру OAuth2 к хранилищеAzов (так же, как Azure Key Vault).

Azure AD позволяет проверить подлинность клиентского приложения с помощью приложения или иноеудостоверения пользователя, а не учетных данных учетной записи. При запуске в Azure можно использовать [удостоверение, управляемое Azure AD.](/azure/active-directory/managed-identities-azure-resources/) Управляемые идентификаторы устраняет необходимость в проверке подлинности клиента и хранении учетных данных в приложении или в приложении.

Azure AD использует элементы управления доступом на основе ролей (RBAC) для управления авторизацией, который также поддерживается Key Vault.

## <a name="service-principal-application-id"></a>Идентификатор основного приложения службы

Арендатор Azure AD предоставляет каждому зарегистрированное приложение [с принципом службы.](/azure/active-directory/develop/developer-glossary#service-principal-object) Директор службы служит идентификатором приложения, который используется во время настройки авторизации для доступа к другим ресурсам Azure через RBAC.

Key Vault — это приложение Майкрософт, предварительно зарегистрированное во всех клиентах Azure AD. Key Vault зарегистрирован в том же идентификаторе приложений в каждом облаке Azure.

| Клиенты | Облако | Идентификатор приложения |
| --- | --- | --- |
| Azure AD | Azure для государственных организаций | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | общедоступный пиринг Azure; | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Другой  | Любой | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого руководства необходимо сначала сделать следующее:

- [Установите Azure CLI](/cli/azure/install-azure-cli).
- [Создание хранилища ключей](quick-create-cli.md)
- [Создайте учетную запись хранения Azure.](../../storage/common/storage-account-create.md?tabs=azure-cli) Имя учетной записи хранилища должно использоваться только буквы и номера в нижнем регистре. Длина имени должна соться от 3 до 24 символов.
      
## <a name="manage-storage-account-keys"></a>Управление ключами учетной записи хранилища

### <a name="connect-to-your-azure-account"></a>Подключение к учетной записи Azure

Authenticate ваш сеанс Azure CLI с помощью команд [входа в az.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Предоставьте Key Vault доступ к учетной записи хранилища

Используйте назначение ролей Azure CLI [az,](/cli/azure/role/assignment?view=azure-cli-latest) чтобы дать ключевой доступ к учетной записи хранилища. Предоставьте команде следующие значения параметров:

- `--role`: Передайте роль RBAC "Ключевой оператор учетной записи" RBAC. Эта роль ограничивает область доступа к учетной записи хранилища. Для классического учетной записи хранения, пройти "Классический учетной записи учетной записи ключевых услуг оператора роль" вместо.
- `--assignee`: Передайтеhttps://vault.azure.netзначение ", которое является URL-адресом для Key Vault в общедоступном облаке Azure. (Для облака Azure Goverment вместо этого используйте '-asingee-object-id' см. [идентификатор основного приложения](#service-principal-application-id)Службы.)
- `--scope`: Передайте идентификатор ресурса `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`учетной записи хранилища, который находится в форме. Чтобы найти идентификатор подписки, используйте команду [списка учетных записей](/cli/azure/account?view=azure-cli-latest#az-account-list) Azure CLI az; Чтобы найти имя учетной записи хранилища и группу ресурсов учетной записи хранения, используйте команду [списка учетных записей](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) хранилища Azure CLI az.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Предоставление разрешения учетной записи пользователя для управляемых учетных записей хранения

Для обновления политики доступа к Ключу Vault и выдачи разрешений учетной записи на хранение учетным записям вашего пользователя и использовании политики [keyvault-set-sslet](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) и предоставления разрешений на учетную запись хранилища.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Обратите внимание, что разрешения для учетных записей хранения не доступны на странице "Политики доступа" учетной записи хранения на портале Azure.
### <a name="create-a-key-vault-managed-storage-account"></a>Создание учетной записи с управлением хранилищем ключей Vault

 Создайте учетную запись хранения с помощью учетной записи хранилища Key Vault с помощью команды [хранения ключей](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) Azure CLI az. Установите период регенерации 90 дней. После 90 дней Key Vault `key1` восстанавливает и свопы активный ключ от `key2` `key1`. `key1`затем помечается как активный ключ. Предоставьте команде следующие значения параметров:

- `--vault-name`: Передайте имя хранилища ключей. Чтобы найти имя хранилища ключей, используйте команду [списка ключей](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) Azure CLI az.
- `-n`: Передайте имя учетной записи хранилища. Чтобы найти имя учетной записи хранилища, используйте команду [списка учетных записей](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) хранилища Azure CLI az.
- `--resource-id`: Передайте идентификатор ресурса `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`учетной записи хранилища, который находится в форме. Чтобы найти идентификатор подписки, используйте команду [списка учетных записей](/cli/azure/account?view=azure-cli-latest#az-account-list) Azure CLI az; Чтобы найти имя учетной записи хранилища и группу ресурсов учетной записи хранения, используйте команду [списка учетных записей](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) хранилища Azure CLI az.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Токены общей подписи доступа

Вы также можете попросить Key Vault создать маркеры общей подписи доступа. Подпись общего доступа обеспечивает делегированный доступ к ресурсам в вашей учетной записи хранения. Вы можете предоставить клиентам доступ к ресурсам в вашей учетной записи хранения без обмена ключами учетной записи. Общая подпись доступа предоставляет вам безопасный способ обмена ресурсами хранения без ущерба для ключей учетной записи.

Команды в этом разделе выполняют следующие действия:

- Установите определение общей `<YourSASDefinitionName>`подписи доступа к учетной записи. Определение устанавливается на учетной записи управляемого хранилища `<YourStorageAccountName>` Key Vault в хранилище `<YourKeyVaultName>`ключей.
- Создайте маркер подписи доступа с общим доступом для служб Blob, File, Table и Queue. Токен создается для типов ресурсов Service, Container и Object. Токен создается со всеми разрешениями, над https, а также с указанными датами начала и окончания.
- Установите управляемое хранилище Key Vault, общее определение подписи доступа в хранилище. Определение имеет шаблон URI созданного маркера общей подписи доступа. Определение имеет общий тип `account` подписи доступа и действителен для N дней.
- Убедитесь, что общая подпись доступа была сохранена в хранилище ключей в качестве секрета.

### <a name="create-a-shared-access-signature-token"></a>Создание маркера общей подписи доступа

Создайте общее определение подписи доступа, используя команду хранения данных Azure CLI [az, генерируемую sas.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) Эта операция `storage` требует `setsas` разрешения и разрешения.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
После успешного запуска операции скопируйте выход.

```console
"se=2020-01-01&sp=***"
```

Этот выход будет передан `--template-id` параметру на следующем этапе.

### <a name="generate-a-shared-access-signature-definition"></a>Создание определения общей подписи доступа

Для создания команды, передающего выход из предыдущего шага к `--template-id` параметру, для создания общего определения подписи доступа используйте [sas-определение хранилища keyvault](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) Az.  Вы можете уговорить имя вашего выбора по параметру. `-n`

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Проверить определение общей подписи доступа

Вы можете проверить, что определение общей подписи доступа хранилось в хранилище ключей, используя [секретный список](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) ключей Azus az az keyvault и команды [секретных шоу az keyvault.](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

Во-первых, найдите общее определение подписи доступа в хранилище ключей, используя команду [секретного списка ключей az.](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list)

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

Секрет, соответствующий вашему определению SAS, будет иметь следующие свойства:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

Теперь вы можете использовать [секретную](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) команду `id` шоу и свойство a s keyvault и свойство для просмотра содержимого этого секрета.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

Выход этой команды покажет строку определения`value`SAS как.


## <a name="next-steps"></a>Следующие шаги

- Узнайте больше о [ключах, секретах и сертификатах.](https://docs.microsoft.com/rest/api/keyvault/)
- Просмотрите статьи в [блоге команды Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
- Ознакомьтесь с справочной документацией [хранилища хранилища az keyvault.](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)
