---
title: Настройка ключей, управляемых клиентом, для учетной записи Azure Cosmos DB
description: Узнайте, как настроить ключи, управляемые клиентом, для учетной записи Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/11/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 964c3e4e2de43e6bcae353f0b525eb62e6613361
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911867"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-db-account"></a>Настройка ключей, управляемых клиентом, для учетной записи Azure Cosmos DB

> [!NOTE]
> В настоящее время необходимо запросить доступ, чтобы использовать эту возможность. Для этого обратитесь в [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com).

Данные, хранящиеся в учетной записи Azure Cosmos DB, автоматически и легко шифруются. Azure Cosmos DB предлагает два варианта управления ключами, используемыми для шифрования неактивных данных:
- **Ключи, управляемые службой**. По умолчанию Корпорация Майкрософт управляет ключами, используемыми для шифрования учетной записи Azure Cosmos DB.
- **Ключи, управляемые клиентом (CMK)** . При необходимости можно добавить второй уровень шифрования с управляемыми ключами.

Ключи, управляемые клиентом, должны храниться в [Azure Key Vault](../key-vault/key-vault-overview.md). Ключ должен быть предоставлен для каждой учетной записи с поддержкой CMK и использоваться для шифрования всех данных, хранящихся в этой учетной записи.

## <a name="setup"></a>Настройка

Сейчас ключи, управляемые клиентом, доступны только для новых учетных записей и должны быть настроены во время создания учетной записи.

### <a name="1-make-sure-the-azure-cosmos-db-resource-provider-is-registered-for-your-azure-subscription"></a>1. Убедитесь, что поставщик ресурсов Azure Cosmos DB зарегистрирован для подписки Azure.

В портал Azure перейдите к своей подписке Azure и выберите **поставщики ресурсов** в меню слева:

![Запись «поставщики ресурсов» в меню слева](./media/how-to-setup-cmk/portal-rp.png)

Найдите поставщик ресурсов **Microsoft. DocumentDB** .
- Если поставщик ресурсов уже помечен как зарегистрированный, ничего делать не нужно.
- Если это не так, выберите его и щелкните **Register**:

    ![Регистрация поставщика ресурсов Microsoft. DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

### <a name="2-configure-your-azure-key-vault-instance"></a>2. Настройка экземпляра Azure Key Vault

Для использования управляемых клиентом ключей с Azure Cosmos DB необходимо установить два свойства в экземпляре Azure Key Vault, который планируется использовать для размещения ключей шифрования: **обратимое удаление** и **не очищать**. Эти свойства не включены по умолчанию, но могут быть включены с помощью PowerShell или Azure CLI.

Сведения о том, как включить эти свойства в существующем экземпляре Azure Key Vault, см. в разделах Включение обратимого удаления и включение защиты от очистки в одной из следующих статей:
- [Использование обратимого удаления с помощью PowerShell](../key-vault/key-vault-soft-delete-powershell.md)
- [Использование обратимого удаления с Azure CLI](../key-vault/key-vault-soft-delete-cli.md)

### <a name="3-add-an-access-policy-to-your-azure-key-vault-instance"></a>3. Добавление политики доступа к экземпляру Azure Key Vault

В портал Azure перейдите к экземпляру Azure Key Vault, который планируется использовать для размещения ключей шифрования. Затем в меню слева выберите **политики доступа** :

!["Политики доступа" в меню слева](./media/how-to-setup-cmk/portal-akv-ap.png)

- Выберите **+ Добавить политику доступа**
- В раскрывающемся меню **разрешения для ключа** выберите **получить**, **расобернуть ключ** и **Ключ переноса**:

    ![Выбор нужных разрешений](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

- В разделе **Выбор субъекта**выберите **нет выбрано**. Затем найдите и выберите участника **Azure Cosmos DB** . Наконец, щелкните **SELECT (выбрать** ) внизу (если не удается найти субъект **Azure Cosmos DB** , возможно, потребуется повторно зарегистрировать поставщик ресурсов **Microsoft. DocumentDB** на шаге 1):

    ![Выбор субъекта Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

- Нажмите кнопку **Добавить** , чтобы добавить новую политику доступа.

### <a name="4-generate-a-key-in-azure-key-vault"></a>4. Создание ключа в Azure Key Vault

В портал Azure перейдите к экземпляру Azure Key Vault, который планируется использовать для размещения ключей шифрования. Затем в меню слева выберите **ключи** .

![Запись "ключи" в меню слева](./media/how-to-setup-cmk/portal-akv-keys.png)

- Выбор **создания/импорта**
- Укажите имя нового ключа, выберите размер ключа RSA (рекомендуется не менее 3072 для обеспечения оптимальной безопасности) и нажмите кнопку **создать**:

    ![Создание нового ключа](./media/how-to-setup-cmk/portal-akv-gen.png)

- После создания ключа щелкните только что созданный ключ, а затем в его текущей версии
- Скопируйте **идентификатор ключа** ключа, кроме части после последней косой черты:

    ![Копирование идентификатора ключа ключа](./media/how-to-setup-cmk/portal-akv-keyid.png)

### <a name="5-create-a-new-azure-cosmos-db-account"></a>5. Создание новой учетной записи Azure Cosmos DB

#### <a name="using-the-azure-portal"></a>Использование портала Azure

При создании новой учетной записи Azure Cosmos DB из портал Azure выберите **ключ, управляемый клиентом** , на шаге **шифрования** . В поле **URI ключа** передайте универсальный код ресурса (uri) Azure Key Vault ключа, скопированного на шаге 4.

![Установка параметров CMK в портал Azure](./media/how-to-setup-cmk/portal-cosmos-enc.png)

#### <a name="using-powershell"></a>Использование PowerShell

При создании новой учетной записи Azure Cosmos DB с помощью PowerShell
- Передайте универсальный код ресурса (URI) Azure Key Vault ключа, скопированный из шага 4 в свойстве **кэйваулткэйури** в **пропертйобжект**,
- обязательно используйте "2019-12-12" в качестве версии API.

> [!IMPORTANT]
> Для успешного создания учетной записи с помощью CMK необходимо явно задать параметр `Location`.

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

#### <a name="using-azure-resource-manager-templates"></a>Использование шаблонов диспетчера ресурсов Azure

При создании новой учетной записи Azure Cosmos DB с помощью шаблона Azure Resource Manager:
- Передайте универсальный код ресурса (URI) Azure Key Vault ключа, скопированный из шага 4 в свойстве **кэйваулткэйури** в объекте **Properties** .
- обязательно используйте "2019-12-12" в качестве версии API

> [!IMPORTANT]
> Для успешного создания учетной записи с помощью CMK необходимо явно задать параметр `location`.

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

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="is-there-any-additional-charge-when-using-customer-managed-keys"></a>Есть ли дополнительная плата за использование ключей, управляемых клиентом?

Да. Чтобы учитывать дополнительную нагрузку, необходимую для управления шифрованием и расшифровкой данных с помощью управляемых клиентом ключей, все операции, выполняемые с учетной записью Azure Cosmos DB, получают увеличение на 25% потребляемых [единиц запросов](./request-units.md) .

### <a name="what-data-gets-encrypted-with-the-cmk"></a>Какие данные шифруются с помощью CMK?

Все данные, хранящиеся в учетной записи Azure Cosmos DB, шифруются с помощью CMK, за исключением следующих метаданных:
- имена [учетных записей, баз данных и контейнеров](./account-overview.md#elements-in-an-azure-cosmos-account)Azure Cosmos DB
- имена [хранимых процедур](./stored-procedures-triggers-udfs.md);
- пути свойств, объявленных в [политиках индексирования](./index-policy.md),
- значения [ключа секции](./partitioning-overview.md)контейнеров.

### <a name="will-customer-managed-keys-be-supported-for-existing-accounts"></a>Будут ли ключи, управляемые клиентом, поддерживаться для существующих учетных записей?

В настоящее время эта функция доступна только для новых учетных записей.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Существует ли план для поддержки более детальной детализации по сравнению с ключами уровня учетной записи?

В настоящее время в настоящий момент учитываются ключи уровня контейнера.

### <a name="how-does-customer-managed-keys-affect-backups"></a>Как управляемые клиентом ключи влияют на резервное копирование?

Azure Cosmos DB выполняет [регулярное и автоматическое резервное копирование](./online-backup-and-restore.md) данных, хранящихся в вашей учетной записи. Эта операция создает резервную копию зашифрованных данных. Для использования восстановленной резервной копии ключ шифрования, используемый во время резервного копирования, по-прежнему должен быть доступен. Это означает, что отзыв не будет выполнен, а версия ключа, использованного во время резервного копирования, останется включенной.

### <a name="how-do-i-revoke-an-encryption-key"></a>Разделы справки отозвать ключ шифрования?

Отзыв ключа выполняется путем отключения последней версии ключа:

![Отключение версии ключа](./media/how-to-setup-cmk/portal-akv-rev2.png)

Кроме того, чтобы отозвать все ключи из экземпляра Azure Key Vault, можно удалить политику доступа, предоставленную участнику Azure Cosmos DB.

![Удаление политики доступа для субъекта-Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Какие операции доступны после отзыва ключа, управляемого клиентом?

Единственная операция, возможная после отмены ключа шифрования, — это удаление учетной записи.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [шифровании данных в Azure Cosmos DB](./database-encryption-at-rest.md)
- Получите общие сведения о [безопасном доступе к данным в Cosmos DB](secure-access-to-data.md)