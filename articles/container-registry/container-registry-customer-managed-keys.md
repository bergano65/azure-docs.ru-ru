---
title: Шифрование на отдыхе с ключами, управляемыми клиентом
description: Узнайте о шифровании на отдыхе в реестре контейнеров Azure и о том, как шифровать свой реестр с помощью ключа, управляемого клиентом, хранящегося в Azure Key Vault
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: 2d5561998cf0b19698c8059a861a4014a171a7e7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461758"
---
# <a name="encryption-using-customer-managed-keys"></a>Шифрование с использованием ключей, управляемых клиентом

При хранении изображений и других артефактов в реестре контейнеров Azure Azure автоматически шифрует содержимое реестра в состоянии покоя [с помощью ключей, управляемых сервисом.](../security/fundamentals/encryption-atrest.md#data-encryption-models) Вы можете дополнить шифрование по умолчанию дополнительным слоем шифрования, используя ключ, который вы создаете и управляете в Azure Key Vault. В этой статье вы просматриваете шаги с помощью Azure CLI и портала Azure.

Шифрование на стороне сервера с ключами, управляемыми клиентом, поддерживается через интеграцию с [Azure Key Vault.](../key-vault/general/overview.md) Можно создавать собственные ключи шифрования и хранить их в хранилище ключей либо использовать API-интерфейсы Azure Key Vault для генерации этих ключей. С помощью Azure Key Vault можно также провести аудит использования ключей.

Эта функция доступна в сервисе реестра контейнеров **Premium.** Для получения информации о уровнях [Azure Container Registry SKUs](container-registry-skus.md)и ограничениях службы реестра см.

> [!IMPORTANT]
> Эта функция в настоящее время находится в предварительном просмотре, и некоторые [ограничения](#preview-limitations) применяются. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.
>
   
## <a name="preview-limitations"></a>Ограничения предварительной версии 

* В настоящее время включить эту функцию можно только при создании реестра.
* После включения ключа, управляемого клиентом, в реестре, вы не можете отключить его.
* [Доверие к содержимому](container-registry-content-trust.md) в настоящее время не поддерживается в реестре, зашифрованном с помощью ключа, управляемого клиентом.
* В реестре, зашифрованном ключом, управляемом клиентом, журналы выполнения [задач ACR](container-registry-tasks-overview.md) в настоящее время сохраняются только в течение 24 часов. Если вам нужно сохранить журналы в течение [export and store task run logs](container-registry-tasks-logs.md#alternative-log-storage)более длительного периода времени, см.

## <a name="prerequisites"></a>Предварительные требования

Для использования шагов Azure CLI в этой статье требуется версия Azure CLI 2.2.0 или позже. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Включить ключ, управляемый клиентом - CLI

### <a name="create-a-resource-group"></a>Создание группы ресурсов

При необходимости запустите [группу az, создайте][az-group-create] команду для создания группы ресурсов для создания хранилища ключей, реестра контейнеров и других необходимых ресурсов.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначаемого пользователем

Создайте назначенную пользователем [управляемую идентификацию для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) с помощью команды [«итнюб».][az-identity-create] Эта идентификация будет использоваться вашим реестром для доступа к службе Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

В выводе команды обратите внимание на `id` следующие значения: и `principalId`. Эти значения нужны в более поздних шагах для настройки доступа к хранилищу ключей.

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

Создайте хранилище ключей с [помощью az keyvault][az-keyvault-create] для хранения управляемого клиентом ключа для шифрования реестра. 

Чтобы предотвратить потерю данных, вызванную случайным удалением ключа или хранилища ключей, необходимо включить следующие настройки: **Soft delete** и **Protection.** Следующий пример включает параметры для следующих параметров: 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Добавление политики доступа к хранилищу ключей

Назначаем политику для хранилища ключей, чтобы идентификационная личность мог получить к нему доступ. В следующей команде [настройки ключей azvault][az-keyvault-set-policy] вы передаете основной идентификатор созданного вами управляемого идентификатора, хранящийся ранее в переменной среды. Установите ключевые разрешения, чтобы **получить,** **unwrapKey,** и **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Создайте ключ и получите идентификатор ключа

Запустите [клавиатуру az keyvault создать][az-keyvault-key-create] команду для создания ключа в хранилище ключей.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

В выводе команды обратите внимание на идентификатор ключа. `kid` Этот идентификатор используется следующим шагом:

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
Для удобства храните это значение в переменной среды:

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Создание реестра с ключом, управляемым клиентом

Запустите [команду az acr][az-acr-create] для создания реестра и включения ключа, управляемого клиентом. Передайте основной идентификатор управляемого удостоверения и идентификатор ключа, хранящийся ранее в переменных среды:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Показать состояние шифрования

Чтобы показать, включено ли шифрование реестра с помощью ключа, управляемого клиентом, запустите команду [шоу-шоу шифрования az acr:][az-acr-encryption-show]

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>Включить ключ, управляемый клиентом - портал

### <a name="create-a-managed-identity"></a>Создание управляемой идентификации

Создайте на портале Azure управенное удостоверение, назначенное [пользователем, для ресурсов Azure.](../active-directory/managed-identities-azure-resources/overview.md) Для шагов [см. Создать удостоверение, назначенное пользователем.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)

Обратите внимание на **название ресурса** управляемой идентификации. Это имя нужно в последующих шагах.

![Создание управляемого удостоверения пользователя на портале Azure](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Для шагов по созданию [Quickstart: Set and retrieve a secret from Azure Key Vault using the Azure portal](../key-vault/secrets/quick-create-portal.md)хранилища ключей см.

При создании хранилища ключей для ключа, управляемого клиентом, во вкладке **Basics** необходимо включить следующие параметры защиты: **Soft delete** и **Protection.** Эти параметры помогают предотвратить потерю данных, вызванную случайными удалениями хранилища ключей или ключей.

![Создание хранилища ключей на портале Azure](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Добавление политики доступа к хранилищу ключей

Назначаем политику для хранилища ключей, чтобы идентификационная личность мог получить к нему доступ.

1. Перейдите к хранилищу ключа.
1. Выберите политики доступа **к настройкам** > **> «Политика доступа».**
1. Выберите **ключевые разрешения**и выберите **Get,** **Unwrap Key**и **Wrap Key.**
1. Выберите **основной принцип** и выберите имя ресурса управляемой идентификации, назначенной пользователем.  
1. Выберите **Добавить,** а затем выберите **Сохранить**.

![Создание политики доступа к хранилищам ключей](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Создание ключа

1. Перейдите к хранилищу ключа.
1. Выберите**параметры** **настройки.** > 
1. Выберите **«Generate/Импорт»** и введите уникальное имя для ключа.
1. Примите оставшиеся значения по умолчанию и выберите **Создать**.
1. После создания выберите ключ и обратите внимание на текущую ключевую версию.

### <a name="create-azure-container-registry"></a>Создание реестра контейнеров Azure

1. Выберите Создать**реестр контейнеров****контейнеров** >  **ресурсов.** > 
1. Во вкладке **"Основы"** выберите или создайте группу ресурсов и введите имя реестра. В **SKU**, выберите **Premium**.
1. Во вкладке **Шифрование** в **ключе, управляемом клиентом,** выберите **Enabled.**
1. В **Identity**выберите созданную вами управляемую идентификацию.
1. В **ключе шифрования** **выберите из Key Vault.**
1. В **ключе Select из** окна Azure Key Vault выберите хранилище ключей, ключ и версию, созданную в предыдущем разделе.
1. Во вкладке **Шифрование** выберите **Обзор и создайте**.
1. Выберите **Создать** для развертывания экземпляра реестра.

![Создание реестра контейнеров на портале Azure](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Чтобы увидеть статус шифрования вашего реестра на портале, перейдите в свой реестр. В **настройках**выберите **шифрование (Предварительный просмотр).**

## <a name="enable-customer-managed-key---template"></a>Включить ключ, управляемый клиентом - шаблон

Вы также можете использовать шаблон Resource Manager для создания реестра и включения шифрования с помощью ключа, управляемого клиентом. 

Следующий шаблон создает новый реестр контейнеров и управляемую идентификатор, назначенную пользователем. Копируйте следующее содержимое в новый файл и `CMKtemplate.json`сохраните его с помощью такого имени файла, как .

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

Выполните следующие действия в предыдущих разделах, чтобы создать следующие ресурсы:

* Хранилище ключей, идентифицированное по имени
* Ключ хранилища, идентифицированный по идентификатору ключа

Выполнить следующее [развертывание группы аз для][az-group-deployment-create] создания реестра с помощью предыдущего файла шаблона. Если указано, укажите новое имя реестра и управляемое имя личности, а также имя хранилища ключей и созданный вами идентификатор ключа. 

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

### <a name="show-encryption-status"></a>Показать состояние шифрования

Чтобы показать состояние шифрования реестра, запустите команду «az acr шифрования show-status» (az-acr-encryption-show-show-status):

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>Использование реестра

После включения реестра для шифрования данных с помощью ключа, управляемого клиентом, вы можете выполнять те же операции реестра, которые вы выполняете в реестре, который не зашифрован ключом, управляемым клиентом. Например, можно проверить подлинность с помощью реестра и нажать изображения Docker. Смотрите пример команд в [Push и вытяните изображение.](container-registry-get-started-docker-cli.md)

## <a name="rotate-key"></a>Поворот ключа

Вы можете повернуть ключ, управляемый клиентом, в Azure Key Vault в соответствии с вашими политиками соответствия. Создайте новый ключ, а затем обновите реестр для шифрования данных с помощью нового ключа. Вы можете выполнить эти действия с помощью Azure CLI или на портале.

Например, запустите [команду создания ключа az keyvault][az-keyvault-key-create] для создания нового ключа:

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

Затем запустите команду [шифрования az acr,][az-acr-encryption-rotate-key] передав новый идентификатор ключа и основной идентификатор управляемой идентификации, которая была настроена ранее:

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>Отозвать ключ

Отмените ключ шифрования, управляемый клиентом, изменив политику доступа в хранилище ключей или удаляя ключ. Например, используйте команду [удаления-политики удаления az keyvault][az-keyvault-delete-policy] для изменения политики доступа управляемой идентификации, используемой вашим реестром. Пример:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Отмена ключа фактически блокирует доступ ко всем данным реестра, так как реестр не может получить доступ к ключу шифрования. Если доступ к ключу включен или удаленный ключ будет восстановлен, ваш реестр подберет ключ, чтобы вы могли снова получить доступ к зашифрованным данным реестра.

## <a name="next-steps"></a>Следующие шаги

* Подробнее о [шифровании в состоянии покоя в Azure](../security/fundamentals/encryption-atrest.md).
* Узнайте больше о политиках доступа и о том, как [обеспечить доступ к хранилищу ключей.](../key-vault/general/secure-your-key-vault.md)
* Чтобы обеспечить обратную связь с ключами, управляемыми клиентами для реестра контейнеров Azure, посетите [сайт ACR GitHub.](https://aka.ms/acr/issues)


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
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
