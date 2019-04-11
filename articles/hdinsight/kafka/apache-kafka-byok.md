---
title: Создание собственных ключей для Apache Kafka в Azure HDInsight (предварительная версия)
description: В этой статье описывается использование собственных ключей из Azure Key Vault для шифрования данных, хранимых в Apache Kafka в Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b5f7c472c8ebd60d8e7f928534834c9672fe3b14
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471313"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight-preview"></a>Создание собственных ключей для Apache Kafka в Azure HDInsight (предварительная версия)

Azure HDInsight включает поддержку создания собственных ключей (BYOK) для Apache Kafka. Эта функция позволяет вам владеть ключами, используемыми для шифрования неактивных данных, и управлять ими. 

Все управляемые диски в HDInsight защищены с помощью шифрования службы хранилища Azure (SSE). По умолчанию данные на этих дисках зашифрованы с помощью ключей, управляемых корпорацией Майкрософт. При включении BYOK необходимо указать ключ шифрования для HDInsight и управлять им с помощью Azure Key Vault. 

Шифрование BYOK — это процедура из одного этапа, выполняемая во время создания кластера без дополнительных затрат. Вам нужно всего лишь зарегистрировать HDInsight в качестве управляемого удостоверения в Azure Key Vault и добавить ключ шифрования при создании кластера.

Все сообщения к кластеру Kafka (включая реплики, обслуживаемые Kafka) шифруются с помощью симметричного ключа шифрования данных (DEK). Ключ DEK защищен с помощью ключа шифрования ключей (KEK) из вашего хранилища ключей. Процессы шифрования и расшифровки полностью обрабатываются Azure HDInsight. 

Для безопасной смены ключей в хранилище ключей можно использовать портал Azure или Azure CLI. При смене ключа кластер Kafka HDInsight начинает использовать новый ключ в течение нескольких минут. Включение функции защиты ключей «Обратимое удаление» обеспечить защиту от программ-шантажистов сценариев или случайного удаления. Без этой функции защиты не поддерживаются хранилища ключей.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-byok"></a>Начало работы с BYOK
Чтобы создать Kafka кластером с поддержкой BYOK, будут рассмотрены следующие действия:
1. Создание управляемых удостоверений для ресурсов Azure
2. Настройка Azure Key Vault и ключей
3. Создание кластера HDInsight Kafka с поддержкой BYOK

## <a name="create-managed-identities-for-azure-resources"></a>Создание управляемых удостоверений для ресурсов Azure

   Для проверки подлинности в хранилище ключей, создание назначаемого пользователем управляемого удостоверения с помощью [портала Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md), или [ Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Дополнительные сведения об управляемых рабочих удостоверений в Azure HDInsight, см. в разделе [управляемые удостоверения в Azure HDInsight](../hdinsight-managed-identities.md). Хотя Azure Active Directory требуется для использования управляемых удостоверений и BYOK в Kafka, корпоративный пакет безопасности (ESP) не является обязательным требованием. Не забудьте сохранить идентификатор ресурса управляемого удостоверения для его добавления в политику доступа Key Vault.

   ![Создание назначаемого пользователем управляемого удостоверения на портале Azure](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="setup-the-key-vault-and-keys"></a>Настройка хранилища ключей и ключи

   HDInsight поддерживает только Azure Key Vault. Если у вас есть собственное хранилище ключей, вы можете импортировать ключи в Azure Key Vault. Помните, что ключи должны быть «Обратимое удаление». Компонент «Обратимое удаление», доступен через интерфейс REST, .NET /C#, интерфейсы, PowerShell и Azure CLI.

   1. Чтобы создать новое хранилище ключей, выполните краткое руководство по [Azure Key Vault](../../key-vault/key-vault-overview.md). Дополнительные сведения об импорте существующих ключей см. в статье [Сведения о ключах, секретах и сертификатах](../../key-vault/about-keys-secrets-and-certificates.md).

   2. Включите «soft-delete» на хранилище ключей с помощью [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) команду интерфейса командной строки.
        ''' Обновление хранилища ключей azure CLI az--имя <Key Vault Name> --enable-soft-delete
        ```

   3. Create keys

        a. To create a new key, select **Generate/Import** from the **Keys** menu under **Settings**.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png)

        b. Set **Options** to **Generate** and give the key a name.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-a-key.png)

        c. Select the key you created from the list of keys.

        ![Azure Key Vault key list](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. When you use your own key for Kafka cluster encryption, you need to provide the key URI. Copy the **Key identifier** and save it somewhere until you're ready to create your cluster.

        ![Copy key identifier](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
    4. Add managed identity to the key vault access policy.
        a. Create a new Azure Key Vault access policy.

        ![Create new Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. Under **Select Principal**, choose the user-assigned managed identity you created.

        ![Set Select Principal for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. Set **Key Permissions** to **Get**, **Unwrap Key**, and **Wrap Key**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

        d. Set **Secret Permissions** to **Get**, **Set**, and **Delete**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

        e. Click on **Save** 

        ![Save Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## Create HDInsight cluster

   You're now ready to create a new HDInsight cluster. BYOK can only be applied to new clusters during cluster creation. Encryption can't be removed from BYOK clusters, and BYOK can't be added to existing clusters.

   ![Kafka disk encryption in Azure portal](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   During cluster creation, provide the full key URL, including the key version. For example, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. You also need to assign the managed identity to the cluster and provide the key URI.

## FAQ for BYOK to Apache Kafka

**How does the Kafka cluster access my key vault?**

   Associate a managed identity with the HDInsight Kafka cluster during cluster creation. This managed identity can be created before or during cluster creation. You also need to grant the managed identity access to the key vault where the key is stored.

**Is this feature available for all Kafka clusters on HDInsight?**

   BYOK encryption is only possible for Kafka 1.1 and above clusters.

**Can I have different keys for different topics/partitions?**

   No, all managed disks in the cluster are encrypted by the same key.

**How can I recover the cluster if the keys are deleted?**

   Since only “Soft Delete” enabled keys are supported, if the keys are recovered in the key vault, the cluster should regain access to the keys. To recover an Azure Key Vault key, see [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) or [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Can I have producer/consumer applications working with a BYOK cluster and a non-BYOK cluster simultaneously?**

   Yes. The use of BYOK is transparent to producer/consumer applications. Encryption happens at the OS layer. No changes need to be made to existing producer/consumer Kafka applications.

**Are OS disks/Resource disks also encrypted?**

   No. OS disks and Resource disks are not encrypted.

**If a cluster is scaled up, will the new brokers support BYOK seamlessly?**

   Yes. The cluster needs access to the key in the key vault during scale up. The same key is used to encrypt all managed disks in the cluster.

**Is BYOK available in my location?**

   Kafka BYOK is available in all public clouds.

## Next steps

* For more information about Azure Key Vault, see [What is Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* To get started with Azure Key Vault, see [Getting Started with Azure Key Vault](../../key-vault/key-vault-overview.md).
