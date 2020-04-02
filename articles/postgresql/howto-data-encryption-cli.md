---
title: Шифрование данных - Azure CLI - для базы данных Azure для PostgreS'L - Единый сервер
description: Узнайте, как настроить и управлять шифрованием данных для базы данных Azure для одного сервера PostgreS'L с помощью Azure CLI.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: c778130a95be1aa0afe81241130b7daf165360c4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522138"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-cli"></a>Шифрование данных для базы данных Azure для единого сервера PostgreS'L с помощью Azure CLI

Узнайте, как использовать Azure CLI для настройки и управления шифрованием данных для базы данных Azure для единого сервера PostgreS'L.

## <a name="prerequisites-for-azure-cli"></a>Предпосылки для Azure CLI

* Подписка Azure и права администратора для нее.
* Создайте хранилище ключей и ключ для использования для ключа, управляемого клиентом. Также включите защиту чистки и мягкое удаление на хранилище ключа.

    ```azurecli-interactive
    az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
    ```

* В созданном Хранилище ключей Azure создайте ключ, который будет использоваться для шифрования данных базы данных Azure для единого сервера PostgreS'L.

    ```azurecli-interactive
    az keyvault key create --name <key_name> -p software --vault-name <vault_name>
    ```

* Для использования существующего хранилища ключей он должен иметь следующие свойства для использования в качестве ключа, управляемого клиентом:
  * [Мягкое удаление](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Очистка защищена](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Ключ должен иметь следующие атрибуты для использования в качестве ключа, управляемого клиентом:
  * без даты окончания срока действия;
  * не отключено;
  * Выполните **получить,** **обернуть** и **развернуть** операции

## <a name="set-the-right-permissions-for-key-operations"></a>Установка правильных разрешений для ключевых операций

1. Существует два способа получения управляемой идентификации для базы данных Azure для единого сервера PostgreS'L.

    ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>Создайте новую базу данных Azure для сервера MyS'L с управляемой идентификацией.

    ```azurecli-interactive
    az postgres server create --name -g <resource_group> --location <locations> --storage-size <size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled>  --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>Обновление существующей базы данных Azure для сервера MyS'L для получения управляемой идентификации.

    ```azurecli-interactive
    az postgres server update –name <server name>  -g <resoure_group> --assign-identity
    ```

2. Установите **ключевые разрешения** (**Получить**, **Оберните**, **Развернуть**) для **главного**, который является именем сервера PostgreS'L одного сервера.

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Установка шифрования данных для базы данных Azure для одного сервера PostgreS'L

1. Включить шифрование данных для базы данных Azure для одного сервера PostgreS'L с помощью ключа, созданного в Хранилище ключей Azure.

    ```azurecli-interactive
    az postgres server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    Ключевой URL:https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Использование шифрования данных для восстановления или реплики серверов

После того, как база данных Azure для одного сервера PostgreS'L зашифрована с помощью управляемого ключа клиента, хранящегося в Key Vault, любая вновь созданная копия сервера также шифруется. Эту новую копию можно сделать либо через локальную или геовосстановительную операцию, либо через операцию реплики (локальный/кросс-регион). Таким образом, для зашифрованного сервера СаугсЗЛ с одним сервером можно использовать следующие шаги для создания зашифрованного восстановленного сервера.

### <a name="creating-a-restoredreplica-server"></a>Создание восстановленного/репликационного сервера

  *  [Создание сервера восстановления](howto-restore-server-cli.md) 
  *  [Создание сервера считываемых реплик](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>После восстановления сервера повторное шифрование данных восстановленного сервера

    ```azurecli-interactive
    az postgres server key create –name  <server name> -g <resource_group> --kid <key url>
    ```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-postgresql-single-server"></a>Дополнительная возможность для ключа, используемого для базы данных Azure для единого сервера PostgreS'L

### <a name="get-the-key-used"></a>Использовать ключ

    ```azurecli-interactive
    az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
    ```

    Key url:  https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

### <a name="list-the-key-used"></a>Список используемого ключа

    ```azurecli-interactive
    az postgres server key list --name  <server name>  -g <resource_group>
    ```

### <a name="drop-the-key-being-used"></a>Удаление используемого ключа

    ```azurecli-interactive
    az postgres server key delete -g <resource_group> --kid <key url> 
    ```
## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Использование шаблона управления ресурсами Azure для обеспечения шифрования данных

Помимо портала Azure, вы также можете включить шифрование данных на базе данных Azure для одного сервера PostgreS'L, используя шаблоны Azure Resource Manager для нового и существующего сервера.

### <a name="for-a-new-server"></a>Для нового сервера

Используйте один из заранее созданных шаблонов Управления ресурсами Azure, чтобы предоставить серверу с включенным шифрованием данных: [Пример с шифрованием данных](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Этот шаблон менеджера ресурсов Azure создает базу данных Azure для одного сервера PostgreS'L и использует **KeyVault** и **Key,** пройденные в качестве параметров, чтобы обеспечить шифрование данных на сервере.

### <a name="for-an-existing-server"></a>Для существующего сервера
Кроме того, можно использовать шаблоны Azure Resource Manager для включения шифрования данных в существующую базу данных Azure для одиночных серверов PostgreS'L.

* Передайте идентификатор ресурсов ключу Убежища Azure, который вы скопировали ранее под свойством `Uri` в объекте свойств.

* Используйте *2020-01-01-предварительный просмотр* в качестве версии API.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Дальнейшие действия

 Чтобы узнать больше о [Azure Database for PostgreSQL Single server data encryption with customer-managed key](concepts-data-encryption-postgresql.md)шифровании данных, см.
