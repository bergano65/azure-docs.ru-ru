---
title: Шифрование неактивных данных с помощью управляемого клиентом ключа
description: Узнайте о шифровании неактивных данных реестра контейнеров Azure и о шифровании реестра уровня "Премиум" с помощью управляемого клиентом ключа, хранящегося в Azure Key Vault
ms.topic: article
ms.date: 05/01/2020
ms.custom: ''
ms.openlocfilehash: 67fb58d0e11709b3d801a81f15d856e9b3db922b
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225892"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Шифрование реестра с помощью управляемого клиентом ключа

При хранении образов и других артефактов в реестре контейнеров Azure содержимое реестра автоматически шифруется, используя [ключи, управляемые службой](../security/fundamentals/encryption-models.md). Шифрование по умолчанию можно расширить с помощью дополнительного уровня шифрования, используя ключ, который вы создаете и которым управляете в Azure Key Vault. Эта статья поможет вам обучиться использованию шифрованию с помощью Azure CLI и портала Azure.

Шифрование на стороне сервера с помощью управляемых клиентом ключей поддерживается с помощью интеграции с [Azure Key Vault](../key-vault/general/overview.md). Вы можете создавать собственные ключи шифрования и хранить их в хранилище ключей либо использовать API-интерфейсы Azure Key Vault для генерации этих ключей. С помощью Azure Key Vault можно также выполнять аудит использования ключа.

Эта возможность доступна на уровне **Премиум** службы реестра контейнеров. Ознакомиться с информацией об уровнях служб реестра и ограничениях можно в статье [Уровни служб Реестра контейнеров Azure](container-registry-skus.md).


## <a name="things-to-know"></a>Полезная информация

* В настоящее время ключ, управляемый клиентом, можно включить только при создании реестра.
* После включения управляемого клиентом ключа в реестр его нельзя отключить.
* [Доверенное содержимое](container-registry-content-trust.md) в настоящее время не поддерживается в реестре, зашифрованном с помощью управляемого клиентом ключа.
* В реестре, зашифрованном с помощью управляемого клиентом ключа, файлы журналов для[задач ACR](container-registry-tasks-overview.md) в настоящее время хранятся только в течение 24 часов. Если вам необходимо хранить файлы журналов дольше, ознакомьтесь с инструкциями по [экспорту и хранению журналов выполнения задач](container-registry-tasks-logs.md#alternative-log-storage).

## <a name="prerequisites"></a>Предварительные требования

Для выполнения действий с Azure CLI, описанных в этой статье, потребуется Azure CLI версии 2.2.0 или более поздней. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Активация управляемых клиентом ключей через CLI

### <a name="create-a-resource-group"></a>Создание группы ресурсов

При необходимости выполните команду [az Group Create][az-group-create], чтобы создать группу ресурсов для создания хранилища ключей, реестра контейнеров и других необходимых ресурсов.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначаемого пользователем

Чтобы создать [управляемое удостоверение, назначаемое пользователем](../active-directory/managed-identities-azure-resources/overview.md), используйте команду [az identity create][az-identity-create]. Это удостоверение будет использоваться реестром для доступа к службе Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

В выходных данных команды обратите внимание на значения `id` и `principalId`. Они будут необходимы в последующих шагах для настройки доступа к хранилищу ключей в реестре.

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

Для удобства можно сохранить эти значения в переменных среды:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Создайте хранилище ключей командой [az keyvault Create][az-keyvault-create] для хранения ключа, управляемого клиентом, используемого для шифрования реестра.

Чтобы предотвратить потери данных, вызванные случайным удалением ключей или хранилища ключей, необходимо включить следующие настройки: **Обратимое удаление** и **Защита от очистки**. В следующем примере приведены параметры для этих настроек.

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Добавьте политику доступа к хранилищу ключей

Настройте политику для хранилища ключей, чтобы идентификатор мог получить к нему доступ. При следующем использовании [команды az keyvault Set-Policy][az-keyvault-set-policy] вы передадите идентификатор субъекта созданного управляемого удостоверения, сохраненного ранее в переменной среды. Задайте разрешения для ключа: **get**, **unwrapKey** и **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

### <a name="create-key-and-get-key-id"></a>Создайте ключ и получите идентификатор ключа

С помощью команды [az keyvault key create][az-keyvault-key-create] создайте ключ в хранилище.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Запомните идентификатор ключа в выходных данных — `kid`. Он потребуется для следующего шага.

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
Для удобства его можно сохранить в переменной среды:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Создайте реестр с использованием управляемого клиентом ключа.

Запустите команду [az acr create][az-acr-create], чтобы создать реестр на уровне службы Premium и включить управляемый клиентом ключ. Передайте идентификатор участника управляемого удостоверения и идентификатор ключа, которые вы сохранили в переменных среды:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Показать статус шифрования

Чтобы узнать, включено ли шифрование реестра с ключом, управляемым клиентом, выполните команду [az acr encryption show][az-acr-encryption-show]:

```azurecli
az acr encryption show --name <registry-name>
```

Она выводит выходные данные следующего вида:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789..."
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Активация управляемых клиентом ключей через портал

### <a name="create-a-managed-identity"></a>Создание управляемого удостоверения

Создайте [управляемое удостоверение](../active-directory/managed-identities-azure-resources/overview.md), назначаемое пользователем, через портал Azure. Подробную инструкцию можно получить на [странице создания управляемого пользователем удостоверения](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Имя удостоверения будет использоваться в последующих шагах.

![Создайте назначаемое пользователем управляемое удостоверение через портал Azure](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Чтобы получить информацию по созданию хранилища ключей, ознакомьтесь с [кратким руководством: Настройка и получение секрета из Azure Key Vault с помощью портала Azure](../key-vault/secrets/quick-create-portal.md).

При создании хранилища ключей для ключа, управляемого клиентом, на вкладке **Основы** включите следующие параметры защиты: **Обратимое удаление** и **Защита от очистки**. Эти настройки помогут предотвратить потери данных, вызванные случайным удалением ключей или хранилища ключей.

![Создайте хранилище ключей через портал Azure](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Добавьте политику доступа к хранилищу ключей

Настройте политику для хранилища ключей, чтобы идентификатор мог получить к нему доступ.

1. Перейдите в службу хранилища ключей.
1. Выберите пункт **Параметры** > **Политика доступа > +Добавить политику доступа**.
1. В разделе **разрешения ключей** выберите **Получение**, **Распаковка ключа** и **Упаковка ключа**.
1. Щелкните на пункт **Назначить основным** и выберите имя ресурса для управляемого удостоверения, назначенного пользователем.  
1. Нажмите кнопку **Добавить**, а затем щелкните **Сохранить**.

![Создайте политику доступа к хранилищу ключей](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Создание ключа

1. Перейдите в службу хранилища ключей.
1. Последовательно выберите **Параметры** > **Ключи**.
1. Выберите **+ создать или импортировать** и введите уникальное имя для ключа.
1. Оставьте остальные значения по умолчанию и выберите **Создать**.
1. После создания выберите ключ и запишите текущую версию ключа.

### <a name="create-azure-container-registry"></a>Создание реестра контейнеров Azure

1. Последовательно выберите **Создать ресурс** > **Контейнеры** > **Реестр контейнеров**.
1. На вкладке **Основы** выберите или создайте группу ресурсов, затем введите имя реестра. В поле **SKU** выберите пункт **Премиум**.
1. На вкладке **шифрование** в разделе **управляемых клиентом ключей** выберите пункт **включено**.
1. В **разделе удостоверений** выберите управляемое удостоверение, которое вы создали.
1. В разделе **Шифрование** нажмите **Выбрать из хранилища ключей**.
1. В окне **Выбрать ключ из Azure Key Vault** выберите хранилище ключей, ключ и версию, созданные в предыдущем разделе.
1. На вкладке **Шифрование** выберите пункт **Проверить + создать**.
1. Нажмите кнопку **Создать**, чтобы активировать реестр.

![Создание реестра контейнеров на портале Azure](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Чтобы просмотреть состояние шифрования реестра на портале, перейдите к реестру. В разделе **Параметры** выберите **Шифрование**.

## <a name="enable-customer-managed-key---template"></a>Активация управляемых клиентом ключей — шаблон

Можно также использовать шаблон диспетчера ресурсов, чтобы создать реестр и включить шифрование с помощью ключа, управляемого клиентом.

Следующий шаблон создаст новый реестр контейнеров и назначаемое пользователем удостоверение. Скопируйте следующее содержимое в новый файл и сохраните его, используя имя файла вида `CMKtemplate.json`.

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

Выполните действия, описанные в предыдущих разделах, чтобы создать следующие ресурсы:

* Хранилище ключей, идентифицируемое по имени
* Ключ хранилища ключей, идентифицируемый по идентификатору ключа

Выполните команду [az group deployment create][az-group-deployment-create], чтобы создать реестр с помощью предыдущего файла шаблона. Укажите в соответствующих полях новое имя реестра и имя управляемого удостоверения, а также созданные вами имя хранилища ключей и идентификатор ключа.

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

### <a name="show-encryption-status"></a>Показать статус шифрования

Чтобы отобразить состояние шифрования реестра, выполните команду [az acr encryption show][az-acr-encryption-show]:

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>Использование реестра

После включения ключа, управляемого клиентом, в реестр, можно выполнять те же операции с реестром, которые выполняются в незашифрованном реестре. Например, можно выполнить проверку подлинности в реестре и отправить образы DOCKER. Примеры команд можно увидеть в [Push and Pull a Image](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Сменить ключ

Регулярная смена управляемого клиентом ключа, используемого для шифрования реестра, происходит с учетом политики соответствия. Создайте новый ключ или обновите версию ключа, затем обновите реестр для шифрования с помощью нового ключа. Это можно сделать с помощью Azure CLI или на портале.

При смене ключа обычно указывается то же удостоверение, которое использовалось при создании реестра. Также вы всегда можете настроить новое пользовательское удостоверение для доступа к ключам или же указать назначенное системой удостоверение для реестра.

> [!NOTE]
> Убедитесь, что для удостоверения, настроенного для доступа к ключу, задана [политика доступа к хранилищу ключей](#add-key-vault-access-policy).

### <a name="azure-cli"></a>Azure CLI

Для создания ключей хранилища или управления ими используйте команду [az keyvault key][az-keyvault-key]. Например, чтобы создать новую версию ключа или ключ, выполните команду [az keyvault key create][az-keyvault-key-create],

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>

# Create new key
az keyvault key create \
  –-name <new-key-name> \
  --vault-name <key-vault-name>
```

затем выполните команду [az acr encryption rotate-key][az-acr-encryption-rotate-key], тем самым передав новый идентификатор ключа и удостоверение, которое необходимо настроить.

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

### <a name="portal"></a>Портал

Используйте настройки **шифрования** реестра, чтобы обновить версию ключа, ключ, хранилище ключей или параметры идентификации, используемые для ключа, управляемого клиентом.

Например, чтобы создать и настроить новую версию ключа, выполните следующие действия.

1. На портале перейдите к нужному реестру.
1. В разделе **Настройки** выберите пункты **Шифрование** > **Изменить ключ**.
1. Нажмите на кнопку **Выбрать ключ**

    ![Смените ключ через портал Azure](./media/container-registry-customer-managed-keys/rotate-key.png)
1. В окне **Выбор ключа из Azure Key Vault** выберите хранилище ключей и ключ, которые вы настроили в предыдущих пунктах, а в разделе **Версия** выберите **Создать новый**.
1. В окне **Создание ключа** нажмите **Сгенерировать**, а затем **Создать**.
1. Завершите выбор ключа и нажмите на кнопку **Сохранить**.

## <a name="revoke-key"></a>Как отозвать ключ

Управляемый клиентом ключ можно отозвать, изменив политику доступа в хранилище или же удалив ключ. Например, используйте команду [az keyvault delete-policy][az-keyvault-delete-policy], чтобы изменить политику доступа управляемого удостоверения, используемого реестром.

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Отзыв ключа мгновенно блокирует доступ ко всем данным реестра, так как реестр не сможет получить доступ к ключу шифрования. Если доступ к ключу будет заново активирован или удаленный ключ будет восстановлен, реестр выберет ключ для повторного получения доступа к зашифрованным данным реестра.

## <a name="advanced-scenarios"></a>Сложные сценарии

### <a name="system-assigned-identity"></a>Назначаемое системой удостоверение

Вы можете настроить назначаемое системой удостоверение реестра для доступа к хранилищу ключей шифрования. Если вы не знаете подробностей об управляемых удостоверениях для ресурсов Azure, [ознакомьтесь с данной статьей](../active-directory/managed-identities-azure-resources/overview.md).

Чтобы включить на портале удостоверение, назначенное системой реестра, выполните следующие действия:

1. На портале перейдите к нужному реестру.
1. Перейдите в пункт **Настройки** >  **Удостоверение**.
1. В разделе **Назначенные системой** задайте для параметра **Состояние** значение **Включено**. Щелкните **Сохранить**.
1. Скопируйте **идентификатор** удостоверения.

Чтобы получить доступ к хранилищу ключей, выполните следующее.

1. Перейдите в службу хранилища ключей.
1. Выберите пункт **Параметры** > **Политика доступа > +Добавить политику доступа**.
1. В разделе **Разрешения ключей** выберите **Получение**, **Распаковка ключа** и **Упаковка ключа**.
1. Выберите пункт **Задать основным** и найдите идентификатор объекта управляемого удостоверения, назначенного системой, или имя реестра.  
1. Нажмите кнопку **Добавить**, а затем щелкните **Сохранить**.

Чтобы обновить параметры шифрования реестра для использования удостоверения, выполните следующие действия.

1. На портале перейдите к нужному реестру.
1. В разделе **Настройки** выберите пункты **Шифрование** > **Изменить ключ**.
1. В разделе **Удостоверения** выберите пункт **Назначенные системой**, затем **Сохранить**.

### <a name="key-vault-firewall"></a>Включение брандмауэра в хранилище ключей

Если хранилище ключей Azure развернуто в виртуальной сети с брандмауэром Key Vault, выполните следующие действия.

1. Настройте шифрование реестра под использование назначенного системой реестра удостоверения. См. предыдущий раздел.
2. Настройте хранилище ключей, чтобы разрешить доступ любыми [доверенными службами](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services).

Подробную инструкцию можно узнать на странице [Настройка брандмауэров и виртуальных сетей Azure Key Vault](../key-vault/general/network-security.md).

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте больше о [Шифровании неактивных данных в Azure](../security/fundamentals/encryption-atrest.md).
* Узнайте больше о политиках доступа и о том, как [обезопасить доступ к хранилищу ключей](../key-vault/general/secure-your-key-vault.md).


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
