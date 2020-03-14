---
title: Шифрование неактивных с помощью управляемых клиентом ключей
description: Узнайте о шифровании неактивных данных реестра контейнеров Azure и о шифровании реестра с помощью управляемого клиентом ключа, хранящегося в Azure Key Vault
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: 7bfc4e9a73280ab330efbeeba51a5dcb0a80da10
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79365347"
---
# <a name="encryption-using-customer-managed-keys"></a>Шифрование с помощью управляемых клиентом ключей

При хранении образов и других артефактов в реестре контейнеров Azure Azure автоматически шифрует содержимое реестра, используя [ключи, управляемые службой](../security/fundamentals/encryption-atrest.md#data-encryption-models). Шифрование по умолчанию можно расширить с помощью дополнительного уровня шифрования, используя ключ, который вы создаете и управляете в Azure Key Vault. В этой статье описано, как использовать Azure CLI и портал Azure.

Шифрование на стороне сервера с помощью управляемых клиентом ключей поддерживается путем интеграции с [Azure Key Vault](../key-vault/key-vault-overview.md). Можно создавать собственные ключи шифрования и хранить их в хранилище ключей либо использовать API-интерфейсы Azure Key Vault для генерации этих ключей. С помощью Azure Key Vault можно также выполнять аудит использования ключа.

Эта функция доступна на уровне службы реестра " **премиум** ". Сведения об уровнях и ограничениях служб реестра см. в статье [номера SKU реестра контейнеров Azure](container-registry-skus.md).

> [!IMPORTANT]
> Эта функция сейчас доступна в предварительной версии, и некоторые [ограничения](#preview-limitations) применяются. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.
>
   
## <a name="preview-limitations"></a>Ограничения предварительной версии 

* В настоящее время эту функцию можно включить только при создании реестра.
* После включения управляемого клиентом ключа в реестр его нельзя отключить.
* В настоящее время [отношение доверия содержимого](container-registry-content-trust.md) не поддерживается в реестре, зашифрованном с помощью управляемого клиентом ключа.
* В реестре, зашифрованном с помощью управляемого клиентом ключа, журналы выполнения для [задач контроля](container-registry-tasks-overview.md) учетных записей в настоящее время сохраняются только 24 часа. Если необходимо хранить журналы дольше, см. инструкции по [экспорту и хранению журналов выполнения задач](container-registry-tasks-logs.md#alternative-log-storage).

## <a name="prerequisites"></a>предварительные требования

Чтобы использовать Azure CLI действия, описанные в этой статье, необходимо Azure CLI версии 2.2.0 или более поздней. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Включение управляемого клиентом ключа — CLI

### <a name="create-a-resource-group"></a>Создание группы ресурсов

При необходимости выполните команду [AZ Group Create][az-group-create] , чтобы создать группу ресурсов для создания хранилища ключей, реестра контейнеров и других необходимых ресурсов.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначаемого пользователем

Создайте назначаемое пользователем [управляемое удостоверение для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) с помощью команды [AZ Identity Create][az-identity-create] . Это удостоверение будет использоваться реестром для доступа к службе Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

В выходных данных команды Обратите внимание на следующие значения: `id` и `principalId`. Эти значения необходимы в последующих шагах для настройки доступа к хранилищу ключей в реестре.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Для удобства храните эти значения в переменных среды:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Создайте хранилище ключей с помощью команды [AZ keyvault Create][az-keyvault-create] , чтобы сохранить ключ, управляемый клиентом, для шифрования реестра. 

Чтобы предотвратить потери данных, вызванные случайным удалением ключей или хранилища ключей, необходимо включить следующие параметры: **обратимое удаление** и **Очистка защиты**. В следующем примере приведены параметры для этих параметров. 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Добавление политики доступа к хранилищу ключей

Настройте политику для хранилища ключей, чтобы идентификатор мог получить к нему доступ. В следующей команде [AZ keyvault Set-Policy][az-keyvault-set-policy] передается идентификатор субъекта созданного управляемого удостоверения, сохраненный ранее в переменной среды. Задайте разрешения для ключа **Get**, **unwrapKey**и **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Создание ключа и получение идентификатора ключа

Выполните команду [AZ keyvault Key Create][az-keyvault-key-create] , чтобы создать ключ в хранилище ключей.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

В выходных данных команды запишите идентификатор ключа `kid`. Этот идентификатор используется на следующем шаге:

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
Для удобства сохраните это значение в переменной среды:

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Создание реестра с ключом, управляемым клиентом

Выполните команду [AZ запись контроля][az-acr-create] доступа, чтобы создать реестр и включить управляемый клиентом ключ. Передайте идентификатор участника управляемого удостоверения и идентификатор ключа, сохраненные ранее в переменных среды:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Отображение состояния шифрования

Чтобы узнать, включено ли шифрование реестра с ключом, управляемым клиентом, выполните команду [AZ запись о шифровании][az-acr-encryption-show] :

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>Включение управляемого клиентом ключа — портала

### <a name="create-a-managed-identity"></a>Создание управляемого удостоверения

Создайте назначаемое пользователем [управляемое удостоверение для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) в портал Azure. Инструкции см. [в разделе Создание назначенного пользователем удостоверения](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Запишите **имя ресурса** управляемого удостоверения. Это имя вам понадобится в последующих шагах.

![Создание управляемого удостоверения, назначенного пользователем, в портал Azure](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Действия по созданию хранилища ключей см. в разделе [Краткое руководство. Установка и извлечение секрета из Azure Key Vault с помощью портал Azure](../key-vault/quick-create-portal.md).

При создании хранилища ключей для управляемого клиентом ключа на вкладке " **базовые** " необходимо включить следующие параметры защиты: **обратимое удаление** и **Защита от**удаления. Эти параметры помогают предотвратить потери данных, вызванные случайными операциями удаления ключей или хранилища ключей.

![Создание хранилища ключей в портал Azure](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Добавление политики доступа к хранилищу ключей

Настройте политику для хранилища ключей, чтобы идентификатор мог получить к нему доступ.

1. Перейдите в хранилище ключей.
1. Выберите **параметры** > **политики доступа > + добавить политику доступа**.
1. Выберите **разрешения для ключа**, а затем выберите **Получение**, **расперенос ключа**и **ключ переносить**.
1. Щелкните **выбрать субъект** и выберите имя ресурса для управляемого удостоверения, назначенного пользователем.  
1. Нажмите кнопку **Добавить**, а затем выберите **сохранить**.

![Создание политики доступа к хранилищу ключей](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Создание ключа

1. Перейдите в хранилище ключей.
1. Последовательно выберите **Параметры** > **Ключи**.
1. Выберите **+ создать/импортировать** и введите уникальное имя для ключа.
1. Примите остальные значения по умолчанию и нажмите кнопку **создать**.
1. После создания выберите ключ и запишите текущую версию ключа.

### <a name="create-azure-container-registry"></a>Создание реестра контейнеров Azure

1. Последовательно выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров**.
1. На вкладке **Основные сведения** выберите или создайте группу ресурсов и введите имя реестра. В списке **SKU**выберите **Premium**.
1. На вкладке **Шифрование** в **разделе управляемый клиентом ключ**выберите **включено**.
1. В поле **удостоверение**выберите созданное управляемое удостоверение.
1. В **разделе ключ шифрования**выберите **выбрать из Key Vault**.
1. В окне **Выбор ключа из Azure Key Vault** выберите хранилище ключей, ключ и версию, созданные в предыдущем разделе.
1. На вкладке **Шифрование** выберите **проверить и создать**.
1. Выберите **создать** , чтобы развернуть экземпляр реестра.

![Создание реестра контейнеров на портале Azure](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Чтобы просмотреть состояние шифрования реестра на портале, перейдите к реестру. В разделе **Параметры**выберите **Шифрование (Предварительная версия)** .

## <a name="enable-customer-managed-key---template"></a>Включение управляемого клиентом ключа-шаблона

Можно также использовать шаблон диспетчер ресурсов, чтобы создать реестр и включить шифрование с помощью ключа, управляемого клиентом. 

Следующий шаблон создает новый реестр контейнеров и управляемое пользователем удостоверение. Скопируйте следующее содержимое в новый файл и сохраните его, используя имя файла, например `CMKtemplate.json`.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}

```

Выполните действия, описанные в предыдущих разделах, чтобы создать следующие ресурсы.

* Хранилище ключей, идентифицируемое по имени
* Ключ хранилища ключей, идентифицируемый по ИДЕНТИФИКАТОРу ключа

Выполните следующую команду [AZ Group Deployment Create][az-group-deployment-create] , чтобы создать реестр с помощью предыдущего файла шаблона. Укажите новое имя реестра и управляемое имя удостоверения, а также имя хранилища ключей и идентификатор ключа, которые вы создали. 

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Отображение состояния шифрования

Чтобы отобразить состояние шифрования реестра, выполните команду [AZ контроля доступа Encryption отобразить-Status] [AZ-контроля доступа-шифрование-отобразить-Status]:

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>Использование реестра

После включения реестра для шифрования данных с помощью управляемого клиентом ключа можно выполнять те же операции с реестром, которые выполняются в реестре, не зашифрованном с помощью управляемого клиентом ключа. Например, можно выполнить проверку подлинности в реестре и отправить образы DOCKER. См. примеры команд в разделе [Push и извлечение образа](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Повернуть ключ

Вы можете поворачивать ключ, управляемый клиентом, в Azure Key Vault в соответствии с политиками соответствия требованиям. Создайте новый ключ, а затем обновите реестр для шифрования данных с помощью нового ключа. Эти действия можно выполнить с помощью Azure CLI или на портале.

Например, выполните команду [AZ keyvault Key Create][az-keyvault-key-create] , чтобы создать новый ключ:

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

Затем выполните команду [AZ записей для шифрования записей контроля доступа][az-acr-encryption-rotate-key] , ПЕРЕДАВ новый идентификатор ключа и идентификатор субъекта ранее настроенного управляемого удостоверения:

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>Отозвать ключ

Отозвать ключ шифрования, управляемый клиентом, можно, изменив политику доступа в хранилище ключей или удалив ключ. Например, используйте команду [AZ keyvault Delete-Policy][az-keyvault-delete-policy] , чтобы изменить политику доступа для управляемого удостоверения, используемого реестром. Пример:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Отмена ключа эффективно блокирует доступ ко всем данным реестра, так как реестр не может получить доступ к ключу шифрования. Если доступ к ключу включен или удаленный ключ восстановлен, реестр выберет ключ, чтобы можно было снова получить доступ к зашифрованным данным реестра.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [шифровании неактивных компонентов в Azure](../security/fundamentals/encryption-atrest.md).
* Дополнительные сведения о политиках доступа и [обеспечении безопасности доступа к хранилищу ключей](../key-vault/key-vault-secure-your-key-vault.md).
* Чтобы отправить отзыв о ключах, управляемых клиентом, для реестра контейнеров Azure, посетите сайт записи контроля доступа на [сайте GitHub](https://aka.ms/acr/issues).


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/keyt#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault/keyt#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault/keyt#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
