---
title: Настройка ключей, управляемых клиентом, для учетной записи Azure Cosmos DB
description: Узнайте, как настроить управляемые клиентами ключи для учетной записи Azure Cosmos DB с помощью Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 733a85e492185e7e83922a3cc91d53c848b939a1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411146"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Налаживайте ключи, управляемые клиентом, для учетной записи Azure Cosmos с помощью Azure Key Vault

> [!NOTE]
> В это время необходимо запросить доступ к этой возможности. Для этого, пожалуйста, свяжитесь с [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com).

Данные, хранящиеся в учетной записи Azure Cosmos, автоматически и бесшовно шифруются ключами, управляемыми корпорацией Майкрософт **(управляемые сервисом ключи).** Дополнительно, вы можете выбрать, чтобы добавить второй уровень шифрования с ключами вы**управляете (клиент управляемых ключей**).

![Слои шифрования вокруг данных клиентов](./media/how-to-setup-cmk/cmk-intro.png)

Вы должны хранить управляемые клиентом ключи в [Azure Key Vault](../key-vault/key-vault-overview.md) и предоставить ключ для каждой учетной записи Azure Cosmos, которая включена с ключами, управляемыми клиентом. Этот ключ используется для шифрования всех данных, хранящихся в этой учетной записи.

> [!NOTE]
> В настоящее время ключи, управляемые клиентами, доступны только для новых учетных записей Azure Cosmos. Вы должны настроить их во время создания учетной записи.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>Зарегистрируйте поставщика ресурсов Azure Cosmos DB для подписки На Azure

1. Войдите на [портал Azure,](https://portal.azure.com/)перейдите на подписку Azure и выберите **поставщиков ресурсов** под вкладкой **«Настройки»:**

   ![Запись "Ресурсные провайдеры" из левого меню](./media/how-to-setup-cmk/portal-rp.png)

1. Поиск поставщика ресурсов **Microsoft.DocumentDB.** Проверьте, зарегистрирован ли поставщик ресурсов как зарегистрированный. Если нет, выберите поставщика ресурсов и выберите **Регистрацию:**

   ![Регистрация поставщика ресурсов Microsoft.DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Наверскакните экземпляр Azure Key Vault

Использование управляемых клиентами ключей с помощью Azure Cosmos DB требует, чтобы вы установили два свойства в экземпляре Azure Key Vault, которые планируется использовать для размещения ключей шифрования. Эти свойства включают **Soft Delete** и Do **Not Purge.** Эти свойства не включены по умолчанию. Вы можете включить их, используя либо PowerShell, либо Azure CLI.

Чтобы узнать, как включить эти свойства в существующем экземпляре Azure Key Vault, смотрите разделы "Soft-delete" и "Защита от очистки" в одной из следующих статей:

- [Как использовать мягкое удаление с PowerShell](../key-vault/key-vault-soft-delete-powershell.md)
- [Как использовать мягкое удаление с Azure CLI](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Добавьте политику доступа в экземпляр Azure Key Vault

1. С портала Azure перейдите в экземпляр Azure Key Vault, который планируется использовать для размещения ключей шифрования. Выберите **политики доступа** из левого меню:

   !["Политики доступа" из левого меню](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Выберите **и добавить политику доступа**.

1. В меню **«Ключевые разрешения»** выключается, выберите **Get,** **Unwrap Key**и **Wrap Key:**

   ![Выбор правильных разрешений](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. В соответствии с **принципом Select**, выберите Нет **выбран.** Затем выберите основной принцип **Azure Cosmos DB** и выберите его (чтобы упростить поиск, вы также можете искать по основному идентификатору: `a232010e-820c-4083-83bb-3ace5fc29d0b` для любого региона Azure, за исключением регионов правительства Azure, где находится `57506a73-e302-42a9-b869-6f12d9ec29e9`основной идентификатор). Наконец, **выберите Выберите** внизу. Если директор **Azure Cosmos DB** не включен в список, возможно, потребуется перерегистрировать поставщика ресурсов **Microsoft.DocumentDB,** как описано в разделе [Регистра поставщика ресурсов](#register-resource-provider) в этой статье.

   ![Выберите принцип Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Выберите **Добавить,** чтобы добавить новую политику доступа.

## <a name="generate-a-key-in-azure-key-vault"></a>Создание ключа в Хранилище ключей Azure

1. С портала Azure перейдите в экземпляр Azure Key Vault, который планируется использовать для размещения ключей шифрования. Затем выберите **ключи** из левого меню:

   ![Запись "Ключи" из левого меню](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Выберите **Generate/Import,** предоставьте имя для нового ключа и выберите размер ключа RSA. Минимум 3072 рекомендуется для лучшей безопасности. Затем выберите **Создать:**

   ![Создание ключа](./media/how-to-setup-cmk/portal-akv-gen.png)

1. После создания ключа выберите вновь созданный ключ, а затем его текущую версию.

1. Копирование ключа **ключ идентификатор**, за исключением части после последнего вперед слэш:

   ![Копирование идентификатора ключа ключа](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Создание новой учетной записи Azure Cosmos

### <a name="using-the-azure-portal"></a>Использование портала Azure

При создании новой учетной записи Azure Cosmos DB на портале Azure выберите **ключ, управляемый клиентом,** на этапе **шифрования.** В поле **Key URI** вставьте идентификатор URI/key ключа ключа Убежища ключей Azure, который вы скопировали с предыдущего шага:

![Настройка параметров CMK на портале Azure](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Использование Azure PowerShell

При создании новой учетной записи Azure Cosmos DB с PowerShell:

- Передайте URI ключа Ключа Azure Key Vault, скопированный ранее под свойством **keyVaultKeyUri** в **PropertyObject.**

- Используйте **версию API 2019-12-12.**

> [!IMPORTANT]
> Необходимо четко `Location` установить параметр для успешного создания учетной записи с помощью ключей, управляемых клиентом.

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

При создании новой учетной записи Azure Cosmos через шаблон управления ресурсами Azure:

- Передайте URI ключа Ключа Azure Vault, который вы скопировали ранее под свойством **keyVaultKeyUri** в объекте **свойств.**

- Используйте **версию API 2019-12-12.**

> [!IMPORTANT]
> Необходимо четко `Location` установить параметр для успешного создания учетной записи с помощью ключей, управляемых клиентом.

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

Развертывание шаблона со следующим скриптом PowerShell:

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

При создании новой учетной записи Azure Cosmos через Azure CLI передайте URI ключа Azure Key Vault, который вы скопировали ранее под параметром **--ключ-ури.**

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

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>Существует ли дополнительная плата за использование ключей, управляемых клиентом?

Да. Для учета дополнительной вычислительной нагрузки, необходимой для управления шифрованием и расшифровкой данных с помощью ключей, управляемых клиентом, все операции, выполняемые в отношении учетной записи Azure Cosmos, увеличиваются на 25 процентов в [единицах запроса.](./request-units.md)

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Какие данные шифруются с помощью ключей, управляемых клиентом?

Все данные, хранящиеся в вашей учетной записи Azure Cosmos, шифруются с помощью ключей, управляемых клиентом, за исключением следующих метаданных:

- Имена [учетных записей, баз данных и контейнеров](./account-overview.md#elements-in-an-azure-cosmos-account) Azure Cosmos DB

- Имена [сохраненных процедур](./stored-procedures-triggers-udfs.md)

- Пути свойств, заявленные в [политике индексирования](./index-policy.md)

- Значения [ключей раздела](./partitioning-overview.md) контейнеров

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Поддерживаются ли управляемые клиентами ключи для существующих учетных записей Azure Cosmos?

Эта функция в настоящее время доступна только для новых учетных записей.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Есть ли план поддержки более тонкой детализации, чем ключи уровня учетной записи?

В настоящее время рассматриваются ключи контейнерного уровня.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Как ключи, управляемые клиентом, влияют на резервную поддержку?

Azure Cosmos DB занимает [регулярное и автоматическое резервное копирование](./online-backup-and-restore.md) данных, хранящихся в вашей учетной записи. Эта операция резервное копирование зашифрованных данных. Для использования восстановленного резервного копирования требуется ключ шифрования, который использовался во время резервного копирования. Это означает, что отзыв не был сделан, и версия ключа, который использовался во время резервного копирования, будет по-прежнему включена.

### <a name="how-do-i-revoke-an-encryption-key"></a>Как отменить ключ шифрования?

Ключевое аннулирование осуществляется путем отключения последней версии ключа:

![Отключить версию ключа](./media/how-to-setup-cmk/portal-akv-rev2.png)

Кроме того, чтобы отозвать все ключи из экземпляра Azure Key Vault, можно удалить политику доступа, предоставленную принципу Azure Cosmos DB:

![Удалять политику доступа для директора Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Какие операции доступны после отзыва ключа, управляемого клиентом?

Единственной возможной операцией при отмене ключа шифрования является удаление учетной записи.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [шифровании данных в Azure Cosmos DB](./database-encryption-at-rest.md).
- Получите обзор [безопасного доступа к данным в Cosmos DB](secure-access-to-data.md).
