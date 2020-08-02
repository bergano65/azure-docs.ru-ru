---
title: Шифрование данных — портал Azure — база данных Azure для MySQL
description: Узнайте, как настроить шифрование данных для базы данных Azure для MySQL и управлять им с помощью портал Azure.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 097e07f3f39fc647d311af5e4eface4a7c6b3675
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498486"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Шифрование данных для базы данных Azure для MySQL с помощью портал Azure

Узнайте, как использовать портал Azure для настройки и управления шифрованием данных для базы данных Azure для MySQL.

## <a name="prerequisites-for-azure-cli"></a>Необходимые условия для Azure CLI

* Подписка Azure и права администратора для нее.
* В Azure Key Vault создайте хранилище ключей и ключ, который будет использоваться для ключа, управляемого клиентом.
* Для использования в качестве ключа, управляемого клиентом, хранилище ключей должно иметь следующие свойства:
  * [Обратимое удаление](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Очистить защищенные](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Ключ должен иметь следующие атрибуты для использования в качестве ключа, управляемого клиентом:
  * без даты окончания срока действия;
  * не отключено;
  * возможность выполнять операции получения, упаковки ключа и распаковки ключа.

## <a name="set-the-right-permissions-for-key-operations"></a>Задайте правильные разрешения для операций с ключами.

1. В Key Vault выберите **политики доступа**  >  **Добавить политику доступа**.

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

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Использование шифрования данных для серверов восстановления или реплик

После шифрования Базы данных Azure для MySQL с помощью управляемого ключа клиента, хранящегося в Key Vault, все создаваемые копии сервера также шифруются. Эту новую копию можно создать с помощью локальной или географической операции либо с помощью операции реплики (локальной или кросс-региона). Для зашифрованного сервера MySQL можно выполнить следующие действия, чтобы создать зашифрованный восстановленный сервер.

1. На сервере выберите **Обзор**  >  **восстановить**.

   ![Снимок экрана базы данных Azure для MySQL с выделенным обзором и восстановлением](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Или для сервера с поддержкой репликации в разделе **Параметры** выберите **репликация**.

   ![Снимок экрана базы данных Azure для MySQL с выделенной репликацией](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. После завершения операции восстановления новый созданный сервер шифруется с помощью ключа основного сервера. Однако функции и параметры на сервере отключены, и сервер недоступен. Это предотвращает обработку любых данных, так как удостоверению нового сервера еще не было предоставлено разрешение на доступ к хранилищу ключей.

   ![Снимок экрана базы данных Azure для MySQL с выделенным состоянием "недоступно"](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Чтобы сделать сервер доступным, повторно проверьте ключ на восстановленном сервере. Выберите ключ повторной проверки **шифрования данных**  >  **Revalidate key**.

   > [!NOTE]
   > Первая попытка повторной проверки завершится ошибкой, так как субъекту-службе нового сервера необходимо предоставить доступ к хранилищу ключей. Чтобы создать субъект-службу, выберите повторно **проверить ключ**, в котором будет отображаться сообщение об ошибке, но создается субъект-служба. После этого ознакомьтесь с [этими действиями](#set-the-right-permissions-for-key-operations) ранее в этой статье.

   ![Снимок экрана базы данных Azure для MySQL с выделенным шагом повторной проверки](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Необходимо предоставить хранилищу ключей доступ к новому серверу.

4. После регистрации субъекта-службы повторно проверьте ключ, и сервер возобновит нормальную работу.

   ![Снимок экрана базы данных Azure для MySQL с восстановленными функциями](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Дальнейшие действия

 Дополнительные сведения о шифровании данных см. в статье [Шифрование данных в базе данных Azure для MySQL с помощью ключа, управляемого клиентом](concepts-data-encryption-mysql.md).
