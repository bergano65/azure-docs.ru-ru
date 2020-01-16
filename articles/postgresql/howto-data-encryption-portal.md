---
title: Шифрование данных для одного сервера базы данных Azure для PostgreSQL с помощью портала
description: Узнайте, как настроить шифрование данных для базы данных Azure для PostgreSQL и управлять им с помощью портал Azure.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 9c935ad8f77e2f8a6198a8ac095e0cc60c025a72
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028637"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-using-portal"></a>Шифрование данных для одного сервера базы данных Azure для PostgreSQL с помощью портала

В этой статье вы узнаете, как настроить шифрование данных для базы данных Azure для PostgreSQL на одном сервере и управлять им с помощью портал Azure.

## <a name="prerequisites-for-cli"></a>Предварительные требования для CLI

* Подписка Azure и права администратора для нее.
* Создайте Azure Key Vault и ключ для использования с ключом, управляемым клиентом.
* Для использования в качестве ключа, управляемого клиентом, хранилище ключей должно иметь следующее свойство:
  * [обратимое удаление](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Очистить защищенные](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Для использования ключа, управляемого клиентом, ключ должен иметь следующие атрибуты.
  * без даты окончания срока действия;
  * не отключено;
  * Возможность выполнять операции _получения_, _переноса ключа_и распаковки _ключей_

## <a name="setting-the-right-permissions-for-key-operations"></a>Установка прав доступа для операций с ключами

1. На Azure Key Vault выберите **политики доступа**, а затем **добавьте политику доступа**.

   ![Общие сведения о политике доступа](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. В разделе **основные разрешения**выберите **получить**, **обернуть**, **распереносить** и **участник**, являющийся именем сервера PostgreSQL. Если сервер-участник не найден в списке существующих участников, его необходимо зарегистрировать, попытайтесь настроить шифрование данных в первый раз, что приведет к сбою.  

   ![Общие сведения о политике доступа](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. **Сохраните** параметры.

## <a name="setting-data-encryption-for-azure-database-for-postgresql-single-server"></a>Настройка шифрования данных для одного сервера базы данных Azure для PostgreSQL

1. В **базе данных Azure для PostgreSQL**выберите **Шифрование данных** , чтобы задать настройку ключа, управляемого клиентом.

   ![Настройка шифрования данных](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Можно выбрать **Key Vault** и пару **ключей** или передать **идентификатор ключа**.

   ![Настройка Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Сохраните** параметры.

4. Чтобы обеспечить полное шифрование всех файлов (включая **временные файлы**), **необходимо** **перезапустить** сервер.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Восстановление или создание реплики сервера с включенным шифрованием данных

После того как база данных Azure для PostgreSQL с одним сервером шифруется с помощью управляемого ключа клиента, хранящегося в Key Vault, все созданные копии сервера, например, операции локального или геовосстановления или реплики (локальной или кросс-региона). Таким образом, для зашифрованного сервера PostgreSQL можно выполнить следующие действия, чтобы создать зашифрованный восстановленный сервер.

1. На сервере выберите **Обзор**, а затем щелкните **восстановить**.

   ![Инициация восстановления](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Или для сервера с поддержкой репликации в разделе **Параметры** выберите **репликация**, как показано ниже:

   ![Инициация реплики](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. После завершения операции восстановления создается новый сервер, который шифруется с помощью ключа основного сервера. Однако функции и параметры на сервере отключены, и сервер помечен в **недоступном** состоянии. Такое поведение предназначено для предотвращения манипуляций с данными, так как удостоверение нового сервера по-прежнему не получило разрешения на доступ к Key Vault.

   ![Пометить сервер как недоступный](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Чтобы исправить недоступное состояние, необходимо повторно проверить ключ на восстановленном сервере. Выберите панель **Шифрование данных** , а затем нажмите кнопку повторно **проверить ключ** .

   > [!NOTE]
   > Первая попытка повторной проверки завершится ошибкой, так как субъекту-службе нового сервера необходимо предоставить доступ к хранилищу ключей. Чтобы создать субъект-службу, выберите повторно **проверить ключ**, который выдаст ошибку, но создаст субъект-службу. После этого ознакомьтесь с шагами [в разделе 2](#setting-the-right-permissions-for-key-operations) выше.

   ![повторно проверить сервер](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Вам потребуется предоставить Key Vault доступ к новому серверу.

4. После регистрации субъекта-службы потребуется повторно проверить ключ, и сервер возобновит нормальную работу.

   ![Стандартный восстановленный сервер](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Дальнейшие действия

 Дополнительные сведения о шифровании данных см. в статье [что такое шифрование данных Azure](concepts-data-encryption-postgresql.md).
