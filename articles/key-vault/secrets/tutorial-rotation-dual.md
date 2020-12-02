---
title: Учебник по смене учетных данных для ресурсов, которые используют два набора учетных данных
description: В этом учебнике описывается, как автоматизировать смену секретов для ресурсов, которые используют два набора учетных данных для аутентификации.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: f208752f13848f0f54648d934d1dfb518e2ea1fd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95500352"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>Автоматизация смены секретов для ресурсов с двумя наборами учетных данных для аутентификации

Лучшим способом проверки подлинности в службах Azure является использование [управляемого удостоверения](../general/authentication.md), но иногда его использование невозможно. В этих случаях используются ключи доступа или пароли. Ключи доступа и пароли следует часто сменять.

В этом учебнике описано, как автоматизировать периодическую смену секретов для баз данных и служб, которые используют два набора учетных данных для аутентификации. В частности, в этом учебнике показано, как выполнять смену ключей учетной записи службы хранилища Azure, сохраненных в Azure Key Vault в виде секретов. Вы будете использовать функцию, активируемую уведомлением Сетки событий Azure. 

> [!NOTE]
> Ключами учетной записи хранения можно автоматически управлять в Key Vault, предоставляя маркеры подписанного URL-адреса для делегированного доступа к учетной записи хранения. Существуют службы, которым требуются строки подключения учетной записи хранения с ключами доступа. Для такого сценария рекомендуется использовать именно это решение.

На следующей схеме показано решение для смены, описанное в этом учебнике: 

![Схема: решение для смены.](../media/secrets/rotation-dual/rotation-diagram.png)

В этом решении Azure Key Vault хранит отдельные ключи доступа учетной записи хранения как версии одного секрета, меняя местами первичный и вторичный ключи в каждой следующей версии. Когда один из ключей доступа сохраняется в последней версии секрета, другой ключ создается повторно и добавляется в Key Vault как новая последняя версия секрета. Такое решение обеспечивает полный цикл смены в приложении для обновления последнего созданного ключа. 

1. За 30 дней до истечения срока действия секрета Key Vault публикует в Сетке событий соответствующее событие.
1. Сетка событий проверяет подписки на события и с помощью HTTP-запроса методом POST вызывает конечную точку приложения-функции, подписанную на это событие.
1. Приложение-функция идентифицирует другой ключ (не назначенный последней версией) и вызывает учетную запись хранения для его повторного создания.
1. Приложение-функция добавляет повторно созданный ключ в Azure Key Vault в качестве новой версии этого секрета.

## <a name="prerequisites"></a>Предварительные требования
* Подписка Azure. [Создайте ее бесплатно.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure Key Vault.
* Две учетные записи хранения Azure.

Если у вас нет хранилища ключей и учетных записей хранения, можно использовать следующую ссылку на развертывание:

[![Ссылка "Развернуть в Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. Для параметра **Группа ресурсов** выберите **Создать**. Присвойте группе имя **akvrotation** и нажмите кнопку **ОК**.
1. Выберите **Review + create** (Просмотреть и создать).
1. Нажмите кнопку **создания**.

    ![Снимок экрана: создание группы ресурсов.](../media/secrets/rotation-dual/dual-rotation-1.png)

Теперь у вас есть хранилище ключей и две учетные записи хранения. Вы можете проверить эти ресурсы в Azure CLI, выполнив следующую команду:

```azurecli
az resource list -o table -g akvrotation
```

Должно отобразиться примерно следующее:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>Создание и развертывание функции смены ключей

Теперь вам нужно создать приложение-функцию с управляемым системой удостоверением и другими обязательными компонентами. Вы также развернете функцию смены для ключей учетной записи хранения.

Для функции смены в приложении-функции требуются следующие компоненты и настройки:
- План службы приложений Azure.
- учетная запись хранения для управления триггерами приложения-функции;
- Политика доступа для доступа к секретам в Key Vault.
- роль службы оператора ключа учетной записи хранения, назначенная приложению-функции, чтобы оно могло обращаться к ключам доступа учетной записи хранения;
- функция смены ключей с активацией по событию и HTTP-триггеру (смена по запросу);
- подписка на событие Сетки событий для события **SecretNearExpiry**.

1. Щелкните ссылку для развертывания шаблона в Azure: 

   [![Ссылка "Развертывание шаблона в Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. В списке **Группа ресурсов** выберите **avkrotation**.
1. В поле **Группа ресурсов учетной записи хранения** введите имя группы ресурсов, в которой расположена ваша учетная запись хранения. Оставьте значение по умолчанию **[resourceGroup().name]** , если ваша учетная запись хранения уже расположена в группе ресурсов, в которой будет развернута функция смены ключа.
1. В поле **Имя учетной записи хранения** введите имя учетной записи хранения, которая содержит сменяемые ключи.
1. В поле **Группа ресурсов Key Vault** введите имя группы ресурсов, в которой расположено ваше хранилище ключей. Оставьте значение по умолчанию **[resourceGroup().name]** , если ваше хранилище ключей уже существует в группе ресурсов, в которой будет развернута функция смены ключа.
1. В поле **Имя Key Vault** введите имя хранилища ключей.
1. В поле **Имя приложения-функции** введите соответствующее имя.
1. В поле **Имя секрета** введите имя секрета, в котором будут храниться ключи доступа.
1. В поле **URL-адрес репозитория** введите расположение кода функции в GitHub: **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** .
1. Выберите **Review + create** (Просмотреть и создать).
1. Нажмите кнопку **создания**.

   ![Снимок экрана: создание первой учетной записи хранения.](../media/secrets/rotation-dual/dual-rotation-2.png)

Выполнив описанные выше действия, вы создадите учетную запись хранения, ферму серверов, приложение-функцию и экземпляр Application Insights. Когда развертывание будет завершено, отобразится следующая страница: ![Снимок экрана: страница "Развертывание выполнено".](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> При возникновении сбоя можно щелкнуть **Повторить развертывание**, чтобы завершить развертывание компонентов.


Шаблоны развертывания и код функции смены можно найти в [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Добавление ключей доступа учетной записи хранения в Key Vault

Для начала настройте политику доступа, которая будет предоставлять пользователям разрешение на **управление секретами**:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Теперь вы можете создать новый секрет, указав в качестве его значения ключ доступа учетной записи хранения. Вам также понадобятся идентификатор ресурса учетной записи хранения, значение срока действия секрета и идентификатор ключа для добавления в секрет, чтобы функция смены смогла повторно создать ключ в учетной записи хранения.

Определите идентификатор ресурса учетной записи хранения. Это значение можно найти в свойстве `id`.
```azurecli
az storage account show -n akvrotationstorage
```

Получите список ключей доступа к учетной записи хранения, чтобы получить значения ключей:

```azurecli
az storage account keys list -n akvrotationstorage 
```

Выполните эту команду, используя полученные значения для `key1Value` и `storageAccountResourceId`:

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Если вы создадите секрет с коротким сроком действия, событие `SecretNearExpiry` будет опубликовано в течение нескольких минут. Это событие активирует функцию для смены секрета.

Чтобы убедиться, что ключи доступа созданы повторно, вы можете получить ключ учетной записи хранения и секрет Key Vault и сравнить их.

Используйте следующую команду, чтобы получить данные о секрете:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
Обратите внимание, что `CredentialId` получает значение другого `keyName`, а `value` создается повторно: ![Снимок экрана: выходные данные команды az keyvault secret show для первой учетной записи хранения.](../media/secrets/rotation-dual/dual-rotation-4.png)

Получите ключи доступа для сравнения значений:
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Снимок экрана: выходные данные команды az storage account keys list для первой учетной записи хранения.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Добавление учетных записей хранения для смены

Вы можете повторно использовать одно и то же приложение-функцию для смены ключей для нескольких учетных записей хранения. 

Чтобы добавить ключи учетной записи хранения в существующую функцию для смены, вам потребуется:
- роль службы оператора ключа учетной записи хранения, назначенная приложению-функции, чтобы оно могло обращаться к ключам доступа учетной записи хранения;
- подписка на событие Сетки событий для события **SecretNearExpiry**.

1. Щелкните ссылку для развертывания шаблона в Azure: 

   [![Ссылка "Развертывание шаблона в Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json)

1. В списке **Группа ресурсов** выберите **avkrotation**.
1. В поле **Имя учетной записи хранения** введите имя учетной записи хранения, которая содержит сменяемые ключи.
1. В поле **Имя Key Vault** введите имя хранилища ключей.
1. В поле **Имя приложения-функции** введите соответствующее имя.
1. В поле **Имя секрета** введите имя секрета, в котором будут храниться ключи доступа.
1. Выберите **Review + create** (Просмотреть и создать).
1. Нажмите кнопку **создания**.

   ![Снимок экрана: создание дополнительной учетной записи хранения.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Добавление дополнительного ключа доступа учетной записи хранения в Key Vault

Определите идентификатор ресурса учетной записи хранения. Это значение можно найти в свойстве `id`.
```azurecli
az storage account show -n akvrotationstorage2
```

Получите список ключей доступа к учетной записи хранения, чтобы получить значения второго ключа:

```azurecli
az storage account keys list -n akvrotationstorage2 
```

Выполните эту команду, используя полученные значения для `key2Value` и `storageAccountResourceId`:

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Используйте следующую команду, чтобы получить данные о секрете:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
Обратите внимание, что `CredentialId` получает значение другого `keyName`, а `value` создается повторно: ![Снимок экрана: выходные данные команды az keyvault secret show для второй учетной записи хранения.](../media/secrets/rotation-dual/dual-rotation-8.png)

Получите ключи доступа для сравнения значений:
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Снимок экрана: выходные данные команды az storage account keys list для второй учетной записи хранения.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-dual-credential-rotation-functions"></a>Функции Key Vault для смены учетных данных

- [Учетная запись хранения](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Кэш Redis](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="next-steps"></a>Дальнейшие действия
- Общие сведения. [Мониторинг Key Vault с помощью Сетки событий Azure](../general/event-grid-overview.md)
- Руководство. [Создание первой функции на портале Azure](../../azure-functions/functions-create-first-azure-function.md)
- Практическое руководство. [Получение сообщения электронной почты при смене секрета в Key Vault](../general/event-grid-logicapps.md)
- Справочные материалы. [Схема событий Сетки событий Azure для Azure Key Vault](../../event-grid/event-schema-key-vault.md)
