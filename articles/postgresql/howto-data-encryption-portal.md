---
title: Шифрование данных - Портал Azure - для базы данных Azure для PostgreS'L - Единый сервер
description: Узнайте, как настроить и управлять шифрованием данных для базы данных Azure для единого сервера PostgreS'L с помощью портала Azure.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: fe4c69787b606c601d2dc8b31cadc6dcf57458da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297073"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Шифрование данных для базы данных Azure для единого сервера PostgreS'L с помощью портала Azure

Узнайте, как использовать портал Azure для настройки и управления шифрованием данных для базы данных Azure для единого сервера PostgreS'L.

## <a name="prerequisites-for-azure-cli"></a>Предпосылки для Azure CLI

* Подписка Azure и права администратора для нее.
* В Azure Key Vault создайте хранилище ключей и ключ для использования для ключа, управляемого клиентом.
* Хранилище ключей должно иметь следующие свойства для использования в качестве ключа, управляемого клиентом:
  * [Мягкое удаление](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Очистка защищена](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Ключ должен иметь следующие атрибуты для использования в качестве ключа, управляемого клиентом:
  * без даты окончания срока действия;
  * не отключено;
  * Возможность выполнять операции get, wrap key и разворачивать ключевые операции

## <a name="set-the-right-permissions-for-key-operations"></a>Установка правильных разрешений для ключевых операций

1. В Key Vault выберите **политики** > доступа**Добавить политику доступа.**

   ![Скриншот Key Vault с политиками доступа и политикой добавления доступа](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Выберите **ключевые разрешения,** и выберите **Получить,** **Оберните,** **Развернуть**, и **основной**, который является названием сервера PostgreS'L. Если директор сервера не может быть найден в списке существующих принципов, его необходимо зарегистрировать. Вам предлагается зарегистрировать доверители сервера при первой попытке настройки шифрования данных, и это не удается.  

   ![Обзор политики доступа](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Нажмите кнопку **Сохранить**.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Установка шифрования данных для базы данных Azure для одного сервера PostgreS'L

1. В базе данных Azure для PostgreS'L выберите **шифрование данных** для настройки ключа, управляемого клиентом.

   ![Скриншот базы данных Azure для PostgreS'L с выделением шифрования данных](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Можно выбрать хранилище ключей и пару ключей, либо ввести идентификатор ключа.

   ![Скриншот базы данных Azure для PostgreS'L с выделенными вариантами шифрования данных](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Нажмите кнопку **Сохранить**.

4. Чтобы обеспечить полную шифрование всех файлов (включая временные файлы), перезаключите сервер.

## <a name="restore-or-create-a-replica-of-the-server"></a>Восстановление или создание реплики сервера

После того, как база данных Azure для одного сервера PostgreS'L зашифрована с помощью управляемого ключа клиента, хранящегося в Key Vault, любая вновь созданная копия сервера также шифруется. Эту новую копию можно сделать либо через локальную или геовосстановительную операцию, либо через операцию реплики (локальный/кросс-регион). Таким образом, для зашифрованного сервера PostgreS'L можно использовать следующие шаги для создания зашифрованного восстановленного сервера.

1. На сервере выберите**Восстановление** **обзора.** > 

   ![Скриншот базы данных Azure для PostgreS'L с обзором и восстановлением выделено](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Или для сервера с поддержкой репликации под заголовком **«Настройки»** выберите **репликацию.**

   ![Скриншот базы данных Azure для PostgreS'L с выделенной репликой](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. После завершения операции восстановления созданный новый сервер шифруется ключом основного сервера. Однако функции и параметры на сервере отключены, а сервер недоступен. Это предотвращает любые манипуляции с данными, поскольку личность нового сервера еще не получила разрешения на доступ к хранилищу ключей.

   ![Скриншот базы данных Azure для PostgreS'L с недоступным статусом](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Чтобы сделать сервер доступным, повторно выключите ключ на восстановленном сервере. Выберите > **ключ revalidate** **шифрования данных.**

   > [!NOTE]
   > Первая попытка повторного подтверждения не увенчается неудачей, так как главному обслуживанию нового сервера необходимо предоставить доступ к хранилищу ключей. Для создания основного сервиса выберите **ключ Revalidate,** который будет отображать ошибку, но генерирует основной сервис. После этого, обратитесь к [этим шагам](#set-the-right-permissions-for-key-operations) ранее в этой статье.

   ![Скриншот базы данных Azure для PostgreS'L с выделенным шагом повторной проверки](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Вам придется предоставить ключу свод доступа к новому серверу.

4. После регистрации основного сервиса повторно переоформить ключ, и сервер возобновляет свою нормальную функциональность.

   ![Скриншот базы данных Azure для PostgreS'L, показывающий восстановленную функциональность](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Использование шаблона управления ресурсами Azure для обеспечения шифрования данных

Помимо портала Azure, вы также можете включить шифрование данных на базе данных Azure для одного сервера PostgreS'L, используя шаблоны Azure Resource Manager для нового и существующего сервера.

### <a name="for-a-new-server"></a>Для нового сервера

Используйте один из заранее созданных шаблонов Управления ресурсами Azure, чтобы предоставить серверу с включенным шифрованием данных: [Пример с шифрованием данных](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

Этот шаблон менеджера ресурсов Azure создает базу данных Azure для одного сервера PostgreS'L и использует **KeyVault** и **Key,** пройденные в качестве параметров, чтобы обеспечить шифрование данных на сервере.

### <a name="for-an-existing-server"></a>Для существующего сервера
Кроме того, можно использовать шаблоны Azure Resource Manager для включения шифрования данных в существующую базу данных Azure для одиночных серверов PostgreS'L.

* Передайте URI ключа ключа Azure Key Vault, `keyVaultKeyUri` который вы скопировали ранее под свойством в объекте свойств.

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
