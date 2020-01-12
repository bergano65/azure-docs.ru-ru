---
title: Шифрование данных для одного сервера базы данных Azure для PostgreSQL с помощью портала
description: Узнайте, как настроить шифрование данных для базы данных Azure для PostgreSQL на одном сервере и управлять им с помощью портал Azure
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: 51c99ca0788900397c922e31e44f121a7ae9caa6
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904267"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-using-portal"></a>Шифрование данных для одного сервера базы данных Azure для PostgreSQL с помощью портала

В этой статье вы узнаете, как настроить шифрование данных для базы данных Azure для PostgreSQL на одном сервере и управлять им с помощью портал Azure.

## <a name="prerequisites-for-powershell"></a>Предварительные требования для PowerShell

* Подписка Azure и права администратора для нее.
* Необходимо установить и запустить Azure PowerShell.
* Создайте Azure Key Vault и ключ для использования с ключом, управляемым клиентом.
* Хранилище ключей должно иметь следующее свойство для использования в качестве ключа, управляемого клиентом
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

2. В разделе **ключевые разрешения** выберите **получить**, **обернуть**, **распереносить** и **участник** , являющийся именем сервера PostgreSQL.

   ![Общие сведения о политике доступа](media/concepts-data-access-and-security-data-encryption/access-policy-warp-unwrap.png)

3. **Сохраните** параметры.

## <a name="setting-data-encryption-for-azure-database-for-postgresql-single-server"></a>Настройка шифрования данных для одного сервера базы данных Azure для PostgreSQL

1. В **базе данных Azure для PostgreSQL**выберите **Шифрование данных** , чтобы установить управляемый клиентом ключ.

   ![Настройка шифрования данных](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Можно выбрать **Key Vault** и пару **ключей** или передать **идентификатор ключа**.

   ![Настройка Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Сохраните** параметры.

4. Чтобы обеспечить полное шифрование всех файлов (включая временные файлы), необходимо перезапустить сервер.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Восстановление или создание реплики сервера, на котором включено шифрование данных

После того как база данных Azure для PostgreSQL с одним сервером шифруется с помощью управляемого ключа клиента, хранящегося в Key Vault, все созданные копии сервера, например, операции локального или геовосстановления или реплики (локальной или кросс-региона). Таким образом, для зашифрованного сервера PostgreSQL можно выполнить следующие действия, чтобы создать зашифрованный восстановленный сервер.

1. На сервере выберите **Обзор**, а затем щелкните **восстановить**.

   ![Инициация восстановления](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Или для сервера с поддержкой репликации в разделе **Параметры** выберите **репликация** .

   ![Инициация реплики](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. После завершения операции восстановления создается новый сервер, который шифруется с помощью ключа основного сервера. Однако функции и параметры на сервере отключены, и сервер помечен в **недоступном** состоянии. Это позволяет предотвратить обработку данных, так как удостоверение нового сервера по-прежнему не получило разрешения на доступ к Key Vault.

   ![Пометить сервер как недоступный](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)


3. Чтобы исправить недоступное состояние, необходимо повторно проверить ключ на восстановленном сервере.

   ![повторно проверить сервер](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Вам потребуется предоставить Key Vault доступ к новому серверу. 

4. После повторной проверки ключа сервер возобновляет свою нормальную работу.

   ![Стандартный восстановленный сервер](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="next-steps"></a>Дальнейшие действия

 Дополнительные сведения о шифровании данных см. в статье [что такое шифрование данных Azure](concepts-data-encryption-postgresql.md).