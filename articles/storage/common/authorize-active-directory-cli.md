---
title: Выполнение команд Azure CLI с учетными данными Azure AD для доступа к данным BLOB-объектов или очередей
titleSuffix: Azure Storage
description: Azure CLI поддерживает вход с использованием учетных данных Azure AD для выполнения команд в хранилище BLOB-объектов Azure и очередей данных. Маркер доступа предоставляется для сеанса и позволяет авторизовать вызов операций. Разрешения зависят от роли RBAC, назначенной субъекту безопасности Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 4133c081823f1cc319480c1bc847b672df0374d4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660620"
---
# <a name="run-azure-cli-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Выполнение команд Azure CLI с учетными данными Azure AD для доступа к данным BLOB-объектов или очередей

Служба хранилища Azure предоставляет расширения для Azure CLI, которые позволяют выполнять команды создания скриптов с учетными данными Azure Active Directory (Azure AD). При входе в Azure CLI с учетными данными Azure AD возвращается маркер доступа OAuth 2,0. Этот маркер автоматически используется Azure CLI для авторизации последующих операций с данными в хранилище BLOB-объектов или очередей. Для поддерживаемых операций больше не требуется передавать ключ учетной записи или маркер SAS с помощью команды.

Вы можете назначать разрешения для данных BLOB-объектов и очередей субъекту безопасности Azure AD через Управление доступом на основе ролей (RBAC). Дополнительные сведения о ролях RBAC в службе хранилища Azure см. в статье [Управление правами доступа к данным службы хранилища Azure с помощью RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Поддерживаемые операции

Расширения службы хранилища Azure поддерживаются для операций с данными BLOB-объектов и очередей. Действия, которые можно вызывать, зависят от разрешений, предоставленных субъекту безопасности Azure AD, с которым вы входите в Azure CLI. Разрешения для контейнеров или очередей службы хранилища Azure назначаются через RBAC. Например, если назначена роль **читателя данных большого двоичного объекта** , можно выполнять команды сценариев, считывающие данные из контейнера или очереди. Если вам назначена роль **участника данных большого двоичного объекта** , можно выполнять команды сценариев, которые считывают, записывают или удаляют контейнер или очередь или содержащиеся в них данные.

Дополнительные сведения о разрешениях, необходимых для каждой операции службы хранилища Azure в контейнере или очереди, см. в разделе [операции хранилища вызовов с токенами OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-azure-cli-commands-using-azure-ad-credentials"></a>Вызов Azure CLI команд с использованием учетных данных Azure AD

Azure CLI поддерживает параметр `--auth-mode` для операций с данными большого двоичного объекта и очереди:

- Задайте для параметра `--auth-mode` значение `login` для входа с помощью субъекта безопасности Azure AD.
- Задайте для параметра `--auth-mode` устаревшее значение `key`, чтобы выполнить попытку запроса ключа учетной записи, если не указаны параметры аутентификации для учетной записи.

В следующем примере показано, как создать контейнер в новой учетной записи хранения из Azure CLI с помощью учетных данных Azure AD. Не забудьте заменить значения заполнителей в угловых скобках собственными значениями.

1. Убедитесь, что установлен Azure CLI версии 2.0.46 или более поздней. Выполните команду `az --version`, чтобы узнать установленную версию.

1. Запустите `az login` и выполните аутентификацию в окне браузера:

    ```azurecli
    az login
    ```

1. Укажите нужную подписку. Создайте группу ресурсов, используя команду [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Создайте учетную запись хранения в этой группе ресурсов с помощью команды [AZ Storage Account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create):

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_ZRS \
        --encryption-services blob
    ```

1. Перед созданием контейнера назначьте себе роль [участника данных BLOB-объекта хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) . Несмотря на то, что вы являетесь владельцем учетной записи, вам нужны явные разрешения на выполнение операций с данными с учетной записью хранения. Дополнительные сведения о назначении ролей RBAC см. [в разделе Предоставление доступа к данным BLOB-объектов и очередей Azure с помощью RBAC в портал Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Назначений ролей RBAC может потребоваться несколько минут для распространения.

1. Чтобы создать контейнер с использованием учетных данных Azure AD, вызовите команду [AZ Storage Container Create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) с параметром `--auth-mode`, для которого задано значение `login`.

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

С параметром `--auth-mode` связана переменная среды `AZURE_STORAGE_AUTH_MODE`. Можно указать соответствующее значение в переменной среды, чтобы не включать его при каждом вызове операции с данными службы хранилища Azure.

## <a name="next-steps"></a>Следующие шаги

- [Использование Azure CLI для назначения роли RBAC доступа к данным BLOB-объектов и очередей](storage-auth-aad-rbac-cli.md)
- [Авторизация доступа к данным BLOB-объектов и очередей с помощью управляемых удостоверений для ресурсов Azure](storage-auth-aad-msi.md)
