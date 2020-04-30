---
title: Настройка ключей, управляемых клиентом, для учетной записи Azure Cosmos DB
description: Узнайте, как настроить ключи, управляемые клиентом, для учетной записи Azure Cosmos DB с помощью Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 8f58887a056c8ca0cd175a44127556562338de38
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81450038"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Настройка ключей, управляемых клиентом, для учетной записи Azure Cosmos с помощью Azure Key Vault

> [!NOTE]
> В настоящее время необходимо запросить доступ, чтобы использовать эту возможность. Для этого обратитесь к [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com).

Данные, хранящиеся в учетной записи Azure Cosmos, автоматически и легко шифруются с помощью ключей, управляемых корпорацией Майкрософт (**ключами, управляемыми службой**). При необходимости можно добавить второй уровень шифрования с управляемыми ключами (**ключи, управляемые клиентом**).

![Уровни шифрования данных клиента](./media/how-to-setup-cmk/cmk-intro.png)

Необходимо хранить ключи, управляемые клиентом, в [Azure Key Vault](../key-vault/general/overview.md) и предоставлять ключ для каждой учетной записи Azure Cosmos, включенной в ключах, управляемых клиентом. Этот ключ используется для шифрования всех данных, хранящихся в этой учетной записи.

> [!NOTE]
> Сейчас ключи, управляемые клиентом, доступны только для новых учетных записей Azure Cosmos. Их следует настроить во время создания учетной записи.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>Регистрация поставщика ресурсов Azure Cosmos DB для подписки Azure

1. Войдите в [портал Azure](https://portal.azure.com/), перейдите к своей подписке Azure и выберите **поставщики ресурсов** на вкладке **Параметры** :

   ![Запись «поставщики ресурсов» в меню слева](./media/how-to-setup-cmk/portal-rp.png)

1. Найдите поставщик ресурсов **Microsoft. DocumentDB** . Убедитесь, что поставщик ресурсов уже помечен как зарегистрированный. В противном случае выберите поставщик ресурсов и нажмите кнопку **зарегистрировать**:

   ![Регистрация поставщика ресурсов Microsoft. DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Настройка экземпляра Azure Key Vault

Использование управляемых клиентом ключей с Azure Cosmos DB требует установки двух свойств в экземпляре Azure Key Vault, который планируется использовать для размещения ключей шифрования. Эти свойства включают **обратимое удаление** и **не выполняют очистку**. Эти свойства не включены по умолчанию. Их можно включить с помощью PowerShell или Azure CLI.

Сведения о том, как включить эти свойства в существующем экземпляре Azure Key Vault, см. в разделах "Включение обратимого удаления" и "Включение защиты от очистки" в одной из следующих статей:

- [Использование обратимого удаления с помощью PowerShell](../key-vault/general/soft-delete-powershell.md)
- [Использование обратимого удаления с Azure CLI](../key-vault/general/soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Добавление политики доступа к экземпляру Azure Key Vault

1. В портал Azure перейдите к экземпляру Azure Key Vault, который планируется использовать для размещения ключей шифрования. В меню слева выберите **политики доступа** :

   !["Политики доступа" в меню слева](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Выберите **+ Добавить политику доступа**.

1. В раскрывающемся меню **разрешения для ключа** выберите **получить**, отменить **Перенос ключа**и включить разрешения для **ключа** .

   ![Выбор нужных разрешений](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. В разделе **Выбор субъекта**выберите **нет выбрано**. Затем найдите субъект **Azure Cosmos DB** и выберите его (чтобы упростить поиск, можно также выполнить поиск по идентификатору участника: `a232010e-820c-4083-83bb-3ace5fc29d0b` для любого региона Azure, за исключением регионов Azure для государственных организаций, где идентификатор участника `57506a73-e302-42a9-b869-6f12d9ec29e9`—). Наконец, нажмите кнопку **выбрать** в нижней части страницы. Если субъект **Azure Cosmos DB** отсутствует в списке, может потребоваться повторная регистрация поставщика ресурсов **Microsoft. DocumentDB** , как описано в разделе [Регистрация поставщика ресурсов](#register-resource-provider) этой статьи.

   ![Выберите субъект Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Нажмите кнопку **Добавить** , чтобы добавить новую политику доступа.

## <a name="generate-a-key-in-azure-key-vault"></a>Создание ключа в Azure Key Vault

1. В портал Azure перейдите к экземпляру Azure Key Vault, который планируется использовать для размещения ключей шифрования. Затем в меню слева выберите **ключи** .

   ![Запись "ключи" в меню слева](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Выберите **создать/импортировать**, введите имя для нового ключа и выберите размер ключа RSA. Для обеспечения оптимальной безопасности рекомендуется не менее 3072. Затем выберите **создать**:

   ![Создание ключа](./media/how-to-setup-cmk/portal-akv-gen.png)

1. После создания ключа выберите только что созданный ключ, а затем его текущую версию.

1. Скопируйте **идентификатор ключа**ключа, кроме части, которая находится после последней косой черты:

   ![Копирование идентификатора ключа ключа](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Создание новой учетной записи Azure Cosmos

### <a name="using-the-azure-portal"></a>Использование портала Azure

При создании новой учетной записи Azure Cosmos DB из портал Azure выберите ключ, **управляемый клиентом** , на шаге **шифрования** . В поле **URI ключа** вставьте идентификатор URI или ключа Azure Key Vault ключ, скопированный на предыдущем шаге.

![Установка параметров CMK в портал Azure](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Использование Azure PowerShell

При создании новой учетной записи Azure Cosmos DB с помощью PowerShell:

- Передайте URI Azure Key Vaultого ключа, скопированный ранее в свойстве **кэйваулткэйури** в **пропертйобжект**.

- Используйте **2019-12-12** в качестве версии API.

> [!IMPORTANT]
> Необходимо явно задать `Location` параметр, чтобы учетная запись была успешно создана с ключами, управляемыми клиентом.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a name="using-an-azure-resource-manager-template"></a>Использование шаблона Azure Resource Manager

При создании новой учетной записи Azure Cosmos с помощью шаблона Azure Resource Manager:

- Передайте URI Azure Key Vaultого ключа, скопированный ранее в свойстве **кэйваулткэйури** в объекте **Properties** .

- Используйте **2019-12-12** в качестве версии API.

> [!IMPORTANT]
> Необходимо явно задать `Location` параметр, чтобы учетная запись была успешно создана с ключами, управляемыми клиентом.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}

```

Разверните шаблон с помощью следующего скрипта PowerShell:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a>Использование Azure CLI

При создании новой учетной записи Azure Cosmos с помощью Azure CLI передайте универсальный код ресурса (URI) Azure Key Vault ключа, скопированный ранее в параметре **--Key-URI** .

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>Взимается ли дополнительная плата за использование управляемых клиентом ключей?

Да. Чтобы учитывать дополнительную загрузку вычислений, необходимую для управления шифрованием и расшифровкой данных с помощью управляемых клиентом ключей, все операции, выполняемые с учетной записью Azure Cosmos, потребляют на 25% [единиц запросов](./request-units.md).

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Какие данные шифруются с помощью управляемых клиентом ключей?

Все данные, хранящиеся в учетной записи Azure Cosmos, шифруются с помощью управляемых клиентом ключей, за исключением следующих метаданных:

- Имена [учетных записей, баз данных и контейнеров](./account-overview.md#elements-in-an-azure-cosmos-account) Azure Cosmos DB

- Имена [хранимых процедур](./stored-procedures-triggers-udfs.md)

- Пути свойств, объявленных в [политиках индексирования](./index-policy.md)

- Значения [ключей разделов](./partitioning-overview.md) контейнеров

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Поддерживаются ли ключи, управляемые клиентом, для существующих учетных записей Azure Cosmos?

В настоящее время эта функция доступна только для новых учетных записей.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Существует ли план для поддержки более детальной детализации по сравнению с ключами уровня учетной записи?

В настоящее время в данный момент учитываются ключи уровня контейнера.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Как управляемые клиентом ключи влияют на резервное копирование?

Azure Cosmos DB выполняет [регулярное и автоматическое резервное копирование](./online-backup-and-restore.md) данных, хранящихся в вашей учетной записи. Эта операция создает резервную копию зашифрованных данных. Чтобы использовать восстановленную резервную копию, требуется ключ шифрования, который использовался во время резервного копирования. Это означает, что отзыв не был выполнен и версия ключа, использованная во время резервного копирования, будет по-прежнему включена.

### <a name="how-do-i-revoke-an-encryption-key"></a>Разделы справки отозвать ключ шифрования?

Отзыв ключа выполняется путем отключения последней версии ключа:

![Отключение версии ключа](./media/how-to-setup-cmk/portal-akv-rev2.png)

Кроме того, чтобы отозвать все ключи из экземпляра Azure Key Vault, можно удалить политику доступа, предоставленную участнику Azure Cosmos DB.

![Удаление политики доступа для субъекта-Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Какие операции доступны после отзыва ключа, управляемого клиентом?

Единственная операция, возможная после отмены ключа шифрования, — это удаление учетной записи.

## <a name="next-steps"></a>Дальнейшие шаги

- Дополнительные сведения о [шифровании данных в Azure Cosmos DB](./database-encryption-at-rest.md).
- Получите общие сведения о [безопасном доступе к данным в Cosmos DB](secure-access-to-data.md).
