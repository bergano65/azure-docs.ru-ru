---
title: Шифрование данных - Портал Azure - База данных Azure для MyS'L
description: Узнайте, как настроить и управлять шифрованием данных для базы данных Azure для MyS'L с помощью портала Azure.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: acf3e6273f98d98d5da55cfb5b044677116c44dc
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520813"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Шифрование данных для базы данных Azure для MyS'L с помощью портала Azure

Узнайте, как использовать портал Azure для настройки и управления шифрованием данных для базы данных Azure для MyS'L.

## <a name="prerequisites-for-azure-cli"></a>Предпосылки для Azure CLI

* Подписка Azure и права администратора для нее.
* В Azure Key Vault создайте хранилище ключей и ключ для использования для ключа, управляемого клиентом.
* Хранилище ключей должно иметь следующие свойства для использования в качестве ключа, управляемого клиентом:
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
  * возможность выполнять операции получения, упаковки ключа и распаковки ключа.

## <a name="set-the-right-permissions-for-key-operations"></a>Установка правильных разрешений для ключевых операций

1. В Key Vault выберите **политики** > доступа**Добавить политику доступа.**

   ![Скриншот Key Vault с политиками доступа и политикой добавления доступа](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Выберите **ключевые разрешения**и выберите **Get,** **Wrap,** **Unwrap**и **Principal**, который является именем сервера MyS'L. Если директор сервера не может быть найден в списке существующих принципов, его необходимо зарегистрировать. Вам предлагается зарегистрировать доверители сервера при первой попытке настройки шифрования данных, и это не удается.

   ![Обзор политики доступа](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Щелкните **Сохранить**.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Установка шифрования данных для базы данных Azure для MyS'L

1. В базе данных Azure для MyS'L выберите **шифрование данных** для настройки ключа, управляемого клиентом.

   ![Скриншот базы данных Azure для MyS'L с выделением шифрования данных](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Можно выбрать хранилище ключей и пару ключей, либо ввести идентификатор ключа.

   ![Скриншот базы данных Azure для MyS'L с выделенными вариантами шифрования данных](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Щелкните **Сохранить**.

4. Чтобы обеспечить полную шифрование всех файлов (включая временные файлы), перезаключите сервер.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Использование шифрования данных для восстановления или реплики серверов

После того, как база данных Azure для MyS'L зашифрована с помощью управляемого ключа клиента, хранящегося в Key Vault, любая вновь созданная копия сервера также шифруется. Эту новую копию можно сделать либо через локальную или геовосстановительную операцию, либо через операцию реплики (локальный/кросс-регион). Таким образом, для зашифрованного сервера MyS'L вы можете использовать следующие шаги для создания зашифрованного восстановленного сервера.

1. На сервере выберите**Восстановление** **обзора.** > 

   ![Скриншот базы данных Azure для MyS'L с обзором и восстановлением выделено](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Или для сервера с поддержкой репликации под заголовком **«Настройки»** выберите **репликацию.**

   ![Скриншот базы данных Azure для MyS'L с выделенной репликой](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. После завершения операции восстановления созданный новый сервер шифруется ключом основного сервера. Однако функции и параметры на сервере отключены, а сервер недоступен. Это предотвращает любые манипуляции с данными, поскольку личность нового сервера еще не получила разрешения на доступ к хранилищу ключей.

   ![Скриншот базы данных Azure для MyS'L с недоступным статусом](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Чтобы сделать сервер доступным, повторно выключите ключ на восстановленном сервере. Выберите > **ключ revalidate** **шифрования данных.**

   > [!NOTE]
   > Первая попытка повторного подтверждения не увенчается неудачей, так как главному обслуживанию нового сервера необходимо предоставить доступ к хранилищу ключей. Для создания основного сервиса выберите **ключ Revalidate,** который будет отображать ошибку, но генерирует основной сервис. После этого, обратитесь к [этим шагам](#set-the-right-permissions-for-key-operations) ранее в этой статье.

   ![Скриншот базы данных Azure для MyS'L с выделенным шагом повторной проверки](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Вам придется предоставить ключу свод доступа к новому серверу.

4. После регистрации основного сервиса повторно переоформить ключ, и сервер возобновляет свою нормальную функциональность.

   ![Скриншот базы данных Azure для MyS'L, показывающий восстановленную функциональность](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Дальнейшие действия

 Чтобы узнать больше о [Azure Database for MySQL data encryption with customer-managed key](concepts-data-encryption-mysql.md)шифровании данных, см.
