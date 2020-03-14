---
title: Шифрование данных — портал Azure — база данных Azure для MySQL
description: Узнайте, как настроить шифрование данных для базы данных Azure для MySQL и управлять им с помощью портал Azure.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 78a290b1e2984719645fb4d4ff253ab021a0826e
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299045"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Шифрование данных для базы данных Azure для MySQL с помощью портал Azure

Узнайте, как использовать портал Azure для настройки и управления шифрованием данных для базы данных Azure для MySQL.

## <a name="prerequisites-for-azure-cli"></a>Необходимые условия для Azure CLI

* Подписка Azure и права администратора для нее.
* В Azure Key Vault создайте хранилище ключей и ключ, который будет использоваться для ключа, управляемого клиентом.
* Для использования в качестве ключа, управляемого клиентом, хранилище ключей должно иметь следующие свойства:
  * [Обратимое удаление](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Очистить защищенные](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Ключ должен иметь следующие атрибуты для использования в качестве ключа, управляемого клиентом:
  * без даты окончания срока действия;
  * не отключено;
  * Возможность выполнять операции получения, переноса ключа, распаковки ключей

## <a name="set-the-right-permissions-for-key-operations"></a>Задайте правильные разрешения для операций с ключами.

1. В Key Vault выберите **политики доступа** > **Добавить политику доступа**.

   ![Снимок экрана Key Vault с выделенными политиками доступа и добавлением политики доступа](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Выберите **ключевые разрешения**и выберите **получить**, **обернуть**, **распереносить**, а **основной**— имя сервера MySQL. Если участник сервера не найден в списке существующих участников, его необходимо зарегистрировать. Вам будет предложено зарегистрировать сервер-участник при первой попытке настроить шифрование данных и выполнить его не удастся.

   ![Общие сведения о политике доступа](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Щелкните **Сохранить**.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Настройка шифрования данных для базы данных Azure для MySQL

1. В базе данных Azure для MySQL выберите **Шифрование данных** , чтобы настроить ключ, управляемый клиентом.

   ![Снимок экрана базы данных Azure для MySQL с выделенным шифрованием данных](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Можно выбрать хранилище ключей и пару ключей или ввести идентификатор ключа.

   ![Снимок экрана базы данных Azure для MySQL с выделенными параметрами шифрования данных](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Щелкните **Сохранить**.

4. Чтобы убедиться, что все файлы (включая временные файлы) полностью зашифрованы, перезапустите сервер.

## <a name="restore-or-create-a-replica-of-the-server"></a>Восстановление или создание реплики сервера

После того как база данных Azure для MySQL шифруется с помощью управляемого ключа клиента, хранящегося в Key Vault, все вновь созданные копии сервера также шифруются. Эту новую копию можно создать с помощью локальной или географической операции либо с помощью операции реплики (локальной или кросс-региона). Для зашифрованного сервера MySQL можно выполнить следующие действия, чтобы создать зашифрованный восстановленный сервер.

1. На сервере выберите **обзор** > **восстановить**.

   ![Снимок экрана базы данных Azure для MySQL с выделенным обзором и восстановлением](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Или для сервера с поддержкой репликации в разделе **Параметры** выберите **репликация**.

   ![Снимок экрана базы данных Azure для MySQL с выделенной репликацией](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. После завершения операции восстановления новый созданный сервер шифруется с помощью ключа основного сервера. Однако функции и параметры на сервере отключены, и сервер недоступен. Это предотвращает обработку любых данных, так как удостоверению нового сервера еще не было предоставлено разрешение на доступ к хранилищу ключей.

   ![Снимок экрана базы данных Azure для MySQL с выделенным состоянием "недоступно"](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Чтобы сделать сервер доступным, повторно проверьте ключ на восстановленном сервере. Выберите **Шифрование данных** > повторно **проверить ключ**.

   > [!NOTE]
   > Первая попытка повторной проверки завершится ошибкой, так как субъекту-службе нового сервера необходимо предоставить доступ к хранилищу ключей. Чтобы создать субъект-службу, выберите повторно **проверить ключ**, в котором будет отображаться сообщение об ошибке, но создается субъект-служба. После этого ознакомьтесь с [этими действиями](#set-the-right-permissions-for-key-operations) ранее в этой статье.

   ![Снимок экрана базы данных Azure для MySQL с выделенным шагом повторной проверки](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Необходимо предоставить хранилищу ключей доступ к новому серверу.

4. После регистрации субъекта-службы повторно проверьте ключ, и сервер возобновит нормальную работу.

   ![Снимок экрана базы данных Azure для MySQL с восстановленными функциями](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Использование шаблона Azure Resource Manager для включения шифрования данных

Помимо портал Azure можно также включить шифрование данных на сервере базы данных Azure для MySQL с помощью шаблонов Azure Resource Manager для новых и существующих серверов.

### <a name="for-a-new-server"></a>Для нового сервера

Используйте один из предварительно созданных шаблонов Azure Resource Manager для подготовки сервера с включенным шифрованием данных: [пример с шифрованием данных](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption) .

Этот шаблон Azure Resource Manager создает сервер базы данных Azure для MySQL и использует **KeyVault** и **ключ** , переданные в качестве параметров для включения шифрования данных на сервере.

### <a name="for-an-existing-server"></a>Для существующего сервера
Кроме того, вы можете использовать шаблоны Azure Resource Manager, чтобы включить шифрование данных на существующих серверах базы данных Azure для MySQL.

* Передайте URI Azure Key Vaultого ключа, скопированный ранее в свойстве `keyVaultKeyUri` объекта Properties.

* Используйте *2020-01-01-Preview* в качестве версии API.

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
      "type": "Microsoft.DBforMySQL/servers",
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
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
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
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
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
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
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

 Дополнительные сведения о шифровании данных см. в статье [Шифрование данных в базе данных Azure для MySQL с помощью ключа, управляемого клиентом](concepts-data-encryption-mysql.md).
