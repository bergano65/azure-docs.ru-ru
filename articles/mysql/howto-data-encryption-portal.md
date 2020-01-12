---
title: Шифрование данных для базы данных Azure для MySQL с помощью портала
description: Узнайте, как настроить шифрование данных для базы данных Azure для MySQL и управлять им с помощью портал Azure
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: 10af869a631b620c2c75aa69722dc03df15f8539
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903851"
---
# <a name="data-encryption-for-azure-database-for-mysql-server-using-azure-portal"></a>Шифрование данных для сервера базы данных Azure для MySQL с помощью портал Azure

В этой статье вы узнаете, как настроить шифрование данных для базы данных Azure для MySQL с помощью портал Azure и управлять им.

## <a name="prerequisites-for-powershell"></a>Предварительные требования для PowerShell

* Подписка Azure и права администратора для нее.
* Необходимо установить и запустить Azure PowerShell.
* Создайте Azure Key Vault и ключ для использования с ключом, управляемым клиентом.
* Для использования в качестве ключа, управляемого клиентом, Key Vault должно иметь следующее свойство
    * [обратимое удаление](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

        ```azurecli-interactive
        az resource update --id $(az keyvault show --name \ <key_valut_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
        ```
    
    * [Очистить защищенные](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)

        ```azurecli-interactive
        az keyvault update --name <key_valut_name> --resource-group <resource_group_name>  --enable-purge-protection true
        ```
* Для использования ключа, управляемого клиентом, ключ должен иметь следующие атрибуты.
    * без даты окончания срока действия;
    * не отключено;
    * Возможность выполнять операции получения, переноса ключа, распаковки ключей

## <a name="setting-the-right-permissions-for-key-operations"></a>Установка прав доступа для операций с ключами

1. На Azure Key Vault выберите **политики доступа** и **Добавить политику доступа** . 

   ![Общие сведения о политике доступа](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Выберите **разрешения ключа** выбрать **получить**, **обернуть**, **распереносить** и **участник** , который является именем сервера MySQL.

   ![Общие сведения о политике доступа](media/concepts-data-access-and-security-data-encryption/access-policy-warp-unwrap.png)

3. **Сохраните** параметры.

## <a name="setting-data-encryption-for-azure-database-for-mysql"></a>Настройка шифрования данных для базы данных Azure для MySQL

1. В **базе данных Azure для MySQL**выберите **Шифрование данных** , чтобы задать настройку ключа, управляемого клиентом.

   ![Настройка шифрования данных](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Можно выбрать **Key Vault** и пару **ключей** или передать **идентификатор ключа**.

   ![Настройка Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Сохраните** параметры.

4. Чтобы обеспечить полное шифрование всех файлов (включая временные файлы), необходимо перезапустить сервер.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Восстановление или создание реплики сервера, на котором включено шифрование данных

После того как база данных Azure для MySQL шифруется с помощью управляемого ключа клиента, хранящегося в Key Vault, любая созданная копия сервера будет сохранена как локальная или геовосстановление, либо как операция репликации (локальная или кросс-регион). Поэтому для зашифрованного сервера MySQL можно выполнить следующие действия, чтобы создать зашифрованный восстановленный сервер.

1. На сервере выберите **Обзор**, а затем щелкните **восстановить**.

   ![Инициация восстановления](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Или для сервера с поддержкой репликации в разделе **Параметры** выберите **репликация** .

   ![Инициация реплики](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. После завершения операции восстановления новый созданный сервер шифруется ключом, используемым для шифрования сервера-источника. Однако функции и параметры на сервере отключены, и сервер помечен в **недоступном** состоянии. Это позволяет предотвратить обработку данных, так как удостоверение нового сервера по-прежнему не получило разрешения на доступ к Key Vault.

   ![Пометить сервер как недоступный](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Чтобы исправить недоступное состояние, необходимо повторно проверить ключ на восстановленном сервере.

   ![повторно проверить сервер](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Вам потребуется предоставить Key Vault доступ к новому серверу. 

4. После повторной проверки ключа сервер возобновляет свою нормальную работу.

   ![Стандартный восстановленный сервер](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="next-steps"></a>Дальнейшие действия

 Дополнительные сведения о шифровании данных см. в статье [что такое шифрование данных Azure](concepts-data-encryption-mysql.md).

