---
title: Шифрование ключей с управлением клиентов для Azure HDInsight
description: В этой статье описывается, как использовать собственный ключ шифрования из Azure Key Vault для шифрования данных, хранящихся на управляемых дисках в кластерах Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 732709dbcb5ebe54025a963379128f1a1e74183e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536307"
---
# <a name="customer-managed-key-disk-encryption"></a>Шифрование управляемых клиентом ключей

Azure HDInsight поддерживает шифрование ключей, управляемых клиентами, для данных на управляемых дисках и ресурсных дисках, прикрепленных к виртуальным машинам кластера HDInsight. Эта функция позволяет использовать Azure Key Vault для управления ключами шифрования, которые обеспечивают безопасность данных в состоянии покоя на кластерах HDInsight.

Все управляемые диски в HDInsight защищены с помощью шифрования службы хранилища Azure (SSE). По умолчанию данные на этих дисках зашифрованы с помощью ключей, управляемых корпорацией Майкрософт. Если вы включите управляемые клиентом ключи для HDInsight, вы предоставляете ключи шифрования для HDInsight для использования и управления этими ключами с помощью Azure Key Vault.

Этот документ не учитывает данные, хранящиеся в учетной записи хранения Azure. Для получения дополнительной информации о [Azure Storage encryption for data at rest](../storage/common/storage-service-encryption.md)шифровании хранилища Azure см. Ваши кластеры могут иметь одну или несколько присоединенных учетных записей Хранилища Azure, где ключи шифрования также могут управляться microsoft или управляться клиентами, но служба шифрования отличается.

## <a name="introduction"></a>Введение

Шифрование ключей, управляемое клиентом, — это одношаговский процесс, обрабатываемый при создании кластера без каких-либо дополнительных затрат. Вам нужно всего лишь зарегистрировать HDInsight в качестве управляемого удостоверения в Azure Key Vault и добавить ключ шифрования при создании кластера.

Как диск ресурса, так и управляемые диски на каждом узлах кластера шифруются симметричным ключом шифрования данных (DEK). Ключ DEK защищен с помощью ключа шифрования ключей (KEK) из вашего хранилища ключей. Процессы шифрования и расшифровки полностью обрабатываются Azure HDInsight.

Если брандмауэр хранилища ключей включен в хранилище ключей, где хранится ключ шифрования диска, IP-адреса регионального поставщика ресурсов HDInsight для региона, где будет развернут кластер, должны быть добавлены к конфигурации брандмауэра хранилища ключей. Это необходимо, поскольку HDInsight не является надежной службой хранилища ключей Azure.

Для безопасной смены ключей в хранилище ключей можно использовать портал Azure или Azure CLI. При повороте ключа кластер HDInsight начинает использовать новый ключ в течение нескольких минут. Включите функции защиты ключей [Soft Delete](../key-vault/general/overview-soft-delete.md) для защиты от сценариев вымогателей и случайного удаления. Ключевые хранилища без этой функции защиты не поддерживаются.

|Тип кластера |OS Disk (управляемый диск) |Диск данных (управляемый диск) |Диск данных Temp (локальный SSD) |
|---|---|---|---|
|Кафка, HBase с ускоренной пишет|[Шифрование SSE](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|Шифрование SSE - Дополнительное шифрование CMK|Дополнительное шифрование CMK|
|Все остальные кластеры (Spark, Interactive, Hadoop, HBase без ускоренного пишет)|Шифрование SSE|Недоступно|Дополнительное шифрование CMK|

## <a name="get-started-with-customer-managed-keys"></a>Начало работы с ключами, управляемыми клиентом

Для создания кластера HDInsight с поддержкой клиента мы пройдем следующие шаги:

1. Создание управляемых идентификаторов для ресурсов Azure
1. Создание хранилища Azure Key Vault
1. Создание ключа
1. Создание политики доступа
1. Создание кластера HDInsight с включенным ключом с управлением клиента
1. Вращение ключа шифрования

## <a name="create-managed-identities-for-azure-resources"></a>Создание управляемых идентификаторов для ресурсов Azure

Создайте назначенную пользователем управляемую идентификацию для проверки подлинности в Key Vault.

Просчитайте [создать назначенную пользователем управляемую идентификацию](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) для определенных шагов. Более подробную информацию о том, как работают управляемые идентификаторы в Azure HDInsight, можно узнать [в Azure HDInsight.](hdinsight-managed-identities.md) Не забудьте сохранить идентификатор ресурса управляемого удостоверения для его добавления в политику доступа Key Vault.

## <a name="create-azure-key-vault"></a>Создание хранилища Azure Key Vault

Создать хранилище ключей. Для конкретных шагов смотрите [создание хранилища ключей Azure.](../key-vault/secrets/quick-create-portal.md)

HDInsight поддерживает только Azure Key Vault. Если у вас есть собственное хранилище ключей, вы можете импортировать ключи в Azure Key Vault. Помните, что хранилище ключей должно иметь **возможность удалить Soft.** Дополнительные сведения об импорте существующих ключей см. в статье [Сведения о ключах, секретах и сертификатах](../key-vault/about-keys-secrets-and-certificates.md).

## <a name="create-key"></a>Создание ключа

1. Из нового хранилища ключей перейдите к **settings** > **Keys** > **и Generate/Import**.

    ![Создание нового ключа в хранилище ключей Azure](./media/disk-encryption/create-new-key.png "Создание нового ключа в хранилище ключей Azure")

1. Укажите имя, а затем выберите **Создать**. Поддержание **ключевого типа** **RSA**по умолчанию.

    ![генерирует ключевое имя](./media/disk-encryption/create-key.png "Создание ключевого имени")

1. Когда вы вернетесь на страницу **Ключей,** выберите созданный ключ.

    ![список ключей хранилища](./media/disk-encryption/key-vault-key-list.png)

1. Выберите версию, чтобы открыть страницу **Key Version.** При использовании собственного ключа для шифрования кластера HDInsight необходимо предоставить ключ URI. Скопируйте **идентификатор ключа** и сохраните его, пока не будете готовы к созданию кластера.

    ![получить идентификатор ключа](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>Создание политики доступа

1. Из нового хранилища ключей перейдите к**политикам доступа** > к >  **параметрам****и добавите политику доступа.**

    ![Создание политики доступа Azure Key Vault.](./media/disk-encryption/key-vault-access-policy.png)

1. На странице **политики добавления** укажите следующую информацию:

    |Свойство |Описание|
    |---|---|
    |Ключевые разрешения|Выберите **Получить,** **Развернуть ключ**, и **оберните ключ**.|
    |Секретные разрешения|Выберите **Получить,** **Установить,** и **удалить**.|
    |Выберите основной|Выберите созданную ранее управляемую изначаную идентификацию, назначенную пользователем.|

    ![Задание выбранного субъекта для политики доступа Azure Key Vault](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Выберите **Добавить**.

1. Щелкните **Сохранить**.

    ![Сохранение политики доступа к ключу Azure Vault](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Создание кластера с помощью шифрования ключей-диска, управляемого клиентом

Теперь можно создать кластер HDInsight. Ключ, управляемый заказчиком, может быть применен только к новым кластерам при создании кластеров. Шифрование не может быть удалено из ключевых кластеров, управляемых клиентами, а ключ, управляемый клиентом, не может быть добавлен к существующим кластерам.

### <a name="using-the-azure-portal"></a>Использование портала Azure

Во время создания кластера предоставьте полный **идентификатор Key,** включая ключевую версию. Например, `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Кроме того, необходимо назначить кластеру управляемое удостоверение и указать URI ключа.

![Создание нового кластера](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Использование Azure CLI

В следующем примере показано, как использовать Azure CLI для создания нового кластера Apache Spark с включенным шифрованием диска. Для получения дополнительной информации, [см Azure CLI az hdinsight создать](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create).

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

### <a name="using-azure-resource-manager-templates"></a>Использование шаблонов диспетчера ресурсов Azure

В следующем примере показано, как использовать шаблон Azure Resource Manager для создания нового кластера Apache Spark с включенным шифрованием диска. Для получения дополнительной [информации](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)см.

Этот пример использует PowerShell для вызова шаблона.

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

Содержимое шаблона управления `azuredeploy.json`ресурсами:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

## <a name="rotating-the-encryption-key"></a>Вращение ключа шифрования

Могут быть сценарии, в которых можно изменить ключи шифрования, используемые кластером HDInsight после его создания. Это можно легко через портал. Для этой операции кластер должен иметь доступ как к текущему ключу, так и к предполагаемому новому ключу, в противном случае операция поворота ключа выйдет из строя.

### <a name="using-the-azure-portal"></a>Использование портала Azure

Чтобы повернуть ключ, вам нужно хранилище базового ключа URI. После того как вы сделали это, перейдите в раздел свойств кластера HDInsight на портале и нажмите на **ключ изменения** под **URL-адресом ключа шифрования диска.** Введите новый ключ URL и представить для поворота ключа.

![вращать ключ шифрования диска](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Использование Azure CLI

Ниже приводится следующий пример, как повернуть ключ шифрования диска для существующего кластера HDInsight. Для получения дополнительной [Azure CLI az hdinsight rotate-disk-encryption-key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key)информации см.

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Часто задаваемые вопросы для шифрования ключей, управляемых клиентами

**Как кластер HDInsight получает доступ к моему хранилищу ключей?**

HDInsight получает доступ к экземпляру Azure Key Vault, используя управляемый итог, который вы связываете с кластером HDInsight. Это управляемое удостоверение можно создать до или во время создания кластера. Вам также потребуется предоставить управляемому удостоверению доступ к хранилищу ключей, где хранится ключ.

**Доступна ли эта функция для всех кластеров HDInsight?**

Шифрование ключей, управляемое клиентами, доступно для всех типов кластеров, кроме Spark 2.1 и 2.2.

**Могу ли я использовать несколько ключей для шифрования различных дисков или папок?**

Нет, все управляемые диски и ресурсные диски шифруются тем же ключом.

**Что произойдет, если кластер потеряет доступ к хранилищу ключа или ключу?**

Если кластер теряет доступ к ключу, предупреждения будут отображаться на портале Apache Ambari. В этом состоянии операция **«Ключ изменения»** выйдет из строя. Как только доступ к ключу будет восстановлен, предупреждения Ambari исчезнут и операции, такие как вращение ключей, могут быть успешно выполнены.

![ключевой доступ Конбдокса Амбари](./media/disk-encryption/ambari-alert.png)

**Как восстановить кластер, если ключи удалены?**

Поскольку поддерживаются только клавиши с включенными "Soft Delete", если ключи восстановлены в хранилище ключей, кластер должен восстановить доступ к ключам. Чтобы восстановить ключ Убежища Azure, [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover)см. [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval)

**Какие типы дисков зашифрованы? Зашифрованы ли также диски/ресурсные диски ОС?**

Ресурсные диски и диски данных/управляемых данных шифруются. Диски ОС не шифруются.

**Если кластер масштабируется, будут ли новые узлы поддерживать клавиши, управляемые клиентами, бесшовно?**

Да. Кластеру требуется доступ к ключу в хранилище ключей во время масштабирования. Один и тот же ключ используется для шифрования управляемых дисков и ресурсных дисков в кластере.

**Доступны ли ключи, управляемые клиентом, в моем местоположении?**

Ключи, управляемые клиентами HDInsight, доступны во всех общедоступных облаках и национальных облаках.

## <a name="next-steps"></a>Следующие шаги

* Для получения дополнительной информации о Убежище ключей Azure [смотрите, что такое Убежище ключей Azure](../key-vault/general/overview.md).
* [Обзор корпоративной безопасности в Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
