---
title: Шифрование данных для базы данных Azure для MySQL с помощью портала
description: Узнайте, как настроить шифрование данных для базы данных Azure для MySQL и управлять им с помощью портал Azure
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: 01c64a6880d671289d02dd36f9e4a9dda2f91131
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922812"
---
# <a name="data-encryption-for-azure-database-for-mysql-server-using-azure-portal"></a>Шифрование данных для сервера базы данных Azure для MySQL с помощью портал Azure

В этой статье вы узнаете, как настроить шифрование данных для базы данных Azure для MySQL с помощью портал Azure и управлять им.

## <a name="prerequisites-for-cli"></a>Предварительные требования для CLI

* Подписка Azure и права администратора для нее.
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

2. Выберите **разрешения на доступ к ключам** , выберите **получить**, **обернуть**, **распереносить** и **участник**, являющийся именем сервера MySQL. Если сервер-участник не найден в списке существующих участников, его необходимо зарегистрировать, попытайтесь настроить шифрование данных в первый раз, что приведет к сбою.

   ![Общие сведения о политике доступа](media/concepts-data-access-and-security-data-encryption/access-policy-warp-unwrap.png)

3. **Сохраните** параметры.

## <a name="setting-data-encryption-for-azure-database-for-mysql"></a>Настройка шифрования данных для базы данных Azure для MySQL

1. В **базе данных Azure для MySQL**выберите **Шифрование данных** , чтобы задать настройку ключа, управляемого клиентом.

   ![Настройка шифрования данных](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Можно выбрать **Key Vault** и пару **ключей** или передать **идентификатор ключа**.

   ![Настройка Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Сохраните** параметры.

4. Чтобы обеспечить полное шифрование всех файлов (включая **временные файлы**), **необходимо** **перезапустить** сервер.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Восстановление или создание реплики сервера с включенным шифрованием данных

После того как база данных Azure для MySQL шифруется с помощью управляемого ключа клиента, хранящегося в Key Vault, любая созданная копия сервера будет сохранена как локальная или геовосстановление, либо как операция репликации (локальная или кросс-регион). Поэтому для зашифрованного сервера MySQL можно выполнить следующие действия, чтобы создать зашифрованный восстановленный сервер.

1. На сервере выберите **Обзор**, а затем щелкните **восстановить**.

   ![Инициация восстановления](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Или для сервера с поддержкой репликации в разделе **Параметры** выберите **репликация** .

   ![Инициация реплики](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. После завершения операции восстановления новый созданный сервер шифруется ключом, используемым для шифрования сервера-источника. Однако функции и параметры на сервере отключены, и сервер помечен в **недоступном** состоянии. Это позволяет предотвратить обработку данных, так как удостоверение нового сервера по-прежнему не получило разрешения на доступ к Key Vault.

   ![Пометить сервер как недоступный](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Чтобы исправить недоступное состояние, необходимо повторно проверить ключ на восстановленном сервере. Щелкните колонку **Шифрование данных** и нажмите кнопку повторно **проверить ключ** .

   > [!NOTE]
   > Первая попытка повторной проверки завершится ошибкой, так как субъекту-службе нового сервера необходимо предоставить доступ к хранилищу ключей. Чтобы создать субъект-службу, щелкните повторно **проверить ключ**, который выдаст ошибку, но создаст субъект-службу. После этого ознакомьтесь с шагами [в разделе 2](https://docs.microsoft.com/azure/mysql/howto-data-encryption-portal#setting-the-right-permissions-for-key-operations) выше.

   ![повторно проверить сервер](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Вам потребуется предоставить Key Vault доступ к новому серверу. 

4. После регистрации субъекта-службы потребуется повторно проверить ключ, и сервер возобновит нормальную работу.

   ![Стандартный восстановленный сервер](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Дальнейшие действия

 Дополнительные сведения о шифровании данных см. в статье [что такое шифрование данных Azure](concepts-data-encryption-mysql.md).

