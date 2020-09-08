---
title: Учебник по смене учетных данных для ресурсов, которые используют два набора учетных данных
description: В этом учебнике описывается, как автоматизировать смену секретов для ресурсов, которые используют два набора учетных данных для аутентификации.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: bf4864e0c6342cbd4729d5b99479eb2ef1a2c48c
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378225"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-two-sets-of-authentication-credentials"></a>Автоматизация смены секретов для ресурсов с двумя наборами учетных данных для аутентификации

Лучшим способом проверки подлинности в службах Azure является использование [управляемого удостоверения](../general/authentication.md), но иногда его использование невозможно. В этих случаях используются ключи доступа или пароли. Ключи доступа и пароли следует часто сменять.

В этом учебнике описано, как автоматизировать периодическую смену секретов для баз данных и служб, которые используют два набора учетных данных для аутентификации. В частности, в этом учебнике с помощью функции, которая запускается уведомлением Сетки событий Azure, выполняется смена ключей учетной записи службы хранилища Azure, сохраненных в Azure Key Vault в виде секретов. , перечислены ниже.

> [!NOTE]
> Ключами учетной записи хранения можно автоматически управлять в Key Vault, предоставляя маркеры подписанного URL-адреса для делегированного доступа к учетной записи хранения. Для некоторых служб требуется строка подключения учетной записи хранения с ключом доступа, и мы рекомендуем использовать это решение в таких ситуациях.

![Схема решения ротации](../media/secrets/rotation-dual/rotation-diagram.png)

В приведенном выше решении Azure Key Vault хранит отдельные ключи доступа в формате версий одного секрета, меняя местами первичный и вторичный ключи в каждой следующей версии. Когда один из ключей доступа сохраняется в последней версии секрета, другой ключ создается повторно и добавляется в Key Vault как новая и последняя версия секрета. Такое решение предоставляет приложениям полный цикл смены секрета для обновления последнего созданного ключа. 

1. За 30 дней до истечения срока действия секрета Key Vault публикует в Сетке событий событие "Срок действия скоро истекает".
1. Сетка событий проверяет подписки на события и с помощью HTTP-запроса методом POST вызывает конечную точку приложения-функции, подписанную на это событие.
1. Приложение-функция выбирает альтернативный ключ (не назначенный последней версией) и вызывает учетную запись хранения для его повторного создания.
1. Приложение-функция добавляет вновь созданный ключ в Azure Key Vault в качестве новой версии этого секрета.

## <a name="prerequisites"></a>Предварительные требования
* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure Key Vault
* Две учетные записи службы хранилища Azure

Если у вас нет существующего хранилища ключей и учетных записей хранения, можно использовать предоставленную ниже ссылку на развертывание.

[![Изображение с кнопкой "Развернуть в Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. Для параметра **Группа ресурсов** выберите **Создать**. Присвойте группе имя **akvrotation** и щелкните **ОК**.
1. Выберите **Просмотр и создание**.
1. Нажмите кнопку **Создать**

    ![Создание группы ресурсов](../media/secrets/rotation-dual/dual-rotation-1.png)

Теперь у вас есть хранилище ключей и две учетные записи хранения. Вы можете проверить эту установку в Azure CLI, выполнив следующую команду:

```azurecli
az resource list -o table -g akvrotation
```

Результат будет выглядеть примерно следующим образом:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-storage-account-key-rotation-function"></a>Создание и развертывание функции смены ключей для учетной записи хранения

Теперь создайте приложение-функцию с управляемым системой удостоверением и другими обязательными компонентами, а затем разверните функции смены ключа для учетной записи хранения.

Для функций смены в приложении-функции требуются следующие компоненты и настройки:
- План службы приложений Azure.
- Учетная запись хранения, которая нужна для управления триггерами приложения-функции.
- Политика доступа для доступа к секретам в Key Vault.
- Назначенная приложению-функции роль службы оператора ключей учетной записи хранения, чтобы оно могло обращаться к ключам доступа учетной записи хранения.
- Функции смены ключей учетной записи хранения с активацией по событию и HTTP-триггеру (смена по запросу).
- Подписка на событие Сетки событий **SecretNearExpiry**.

1. Щелкните ссылку для развертывания шаблона в Azure: 

   [![Изображение с кнопкой "Развернуть в Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. В списке **Группа ресурсов** выберите **avkrotation**.
1. В поле **Имя учетной записи хранения** введите имя учетной записи хранения, в которой нужно сменить ключи доступа.
1. В поле **Имя Key Vault** введите имя хранилища ключей.
1. В поле **Имя приложения-функции** введите имя приложения-функции.
1. В поле **Имя секрета** введите имя секрета, где будут храниться ключи доступа.
1. В поле **URL-адрес репозитория** введите расположение кода для функции на сайте GitHub ( **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** ).
1. Выберите **Просмотр и создание**.
1. Нажмите кнопку **Создать**

   ![Проверка и создание первой учетной записи хранения](../media/secrets/rotation-dual/dual-rotation-2.png)

Выполнив описанные выше действия, вы создадите учетную запись хранения, ферму серверов, приложение-функцию и Application Insights. После завершения развертывания вы увидите следующий экран: ![Развертывание завершено](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> В случае любых сбоев можно нажать кнопку **Развернуть повторно**, чтобы завершить развертывание остальных компонентов.


Шаблоны развертывания и код функций смены можно найти в репозитории [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-storage-account-access-key-to-key-vault"></a>Добавление в Key Vault ключа доступа к учетной записи хранения

Для начала настройте политику доступа, которая будет предоставлять пользователям разрешение на *управление секретами*:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Теперь вы можете создать новый секрет, указав в качестве его значения ключ доступа к учетной записи хранения. Вам также потребуется идентификатор ресурса учетной записи хранения, срок действия секрета и идентификатор ключа для добавления в секрет, чтобы функция смены смогла повторно создать ключ в учетной записи хранения.

Получите идентификатор ресурса учетной записи хранения. Это значение можно найти в свойстве `id`:
```azurecli
az storage account show -n akvrotationstorage
```

Получите список ключей доступа к учетной записи хранения, чтобы извлечь значения ключа:

```azurecli
az storage account keys list -n akvrotationstorage 
```

Заполните полученными значениями переменные **key1Value** и **storageAccountResourceId**:

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

При создании секрета с коротким сроком действия через несколько минут публикуется событие `SecretNearExpiry`, которое вызывает функцию для смены секрета.

Чтобы убедиться, что созданы новые ключи доступа, вы можете извлечь и сравнить ключи учетной записи хранения и секрет Key Vault.

Чтобы отобразить сведения о секретах, используйте следующую команду:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
Обратите внимание, что `CredentialId` получает значение альтернативного `keyName`, а `value` создается повторно. ![Выходные данные команды az keyvault secret show для первой учетной записи хранения](../media/secrets/rotation-dual/dual-rotation-4.png)

Получите ключи доступа для проверки значения:
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Выходные данные команды az storage account keys list для первой учетной записи хранения](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-additional-storage-accounts-for-rotation"></a>Добавление дополнительных учетных записей хранения для смены секретов

Одно приложение-функцию можно применить для смены секретов в нескольких учетных записях хранения. 

Чтобы добавить в существующую функцию дополнительные ключи учетной записи хранения, которые нужно сменить, вам потребуется следующее:
- Назначенная приложению-функции роль службы оператора ключей учетной записи хранения, чтобы оно могло обращаться к ключам доступа учетной записи хранения.
- Подписка на событие Сетки событий **SecretNearExpiry**.

1. Щелкните ссылку для развертывания шаблона в Azure: 

   [![Изображение с кнопкой "Развернуть в Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json)

1. В списке **Группа ресурсов** выберите **avkrotation**.
1. В поле **Имя учетной записи хранения** введите имя учетной записи хранения, в которой нужно сменить ключи доступа.
1. В поле **Имя Key Vault** введите имя хранилища ключей.
1. В поле **Имя приложения-функции** введите имя приложения-функции.
1. В поле **Имя секрета** введите имя секрета, где будут храниться ключи доступа.
1. Выберите **Просмотр и создание**.
1. Нажмите кнопку **Создать**

   ![Проверка и создание второй учетной записи хранения](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Добавление в Key Vault дополнительного ключа доступа к учетной записи хранения

Получите идентификатор ресурса учетной записи хранения. Это значение можно найти в свойстве `id`:
```azurecli
az storage account show -n akvrotationstorage2
```

Получите список ключей доступа к учетной записи хранения, чтобы извлечь значение второго ключа:

```azurecli
az storage account keys list -n akvrotationstorage2 
```

Заполните полученными значениями переменные **key2Value** и **storageAccountResourceId**.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Отобразите сведения о секретах с помощью следующей команды:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
Обратите внимание, что `CredentialId` получает значение альтернативного `keyName`, а `value` создается повторно. ![Выходные данные команды az keyvault secret show для второй учетной записи хранения](../media/secrets/rotation-dual/dual-rotation-8.png)

Получите ключи доступа для проверки значения:
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Выходные данные команды az storage account keys list для второй учетной записи хранения](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="available-key-vault-dual-credential-rotation-functions"></a>Доступные функции Key Vault для смены дублирующихся учетных данных

- [Учетная запись хранения](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Кэш Redis](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="learn-more"></a>Дополнительные сведения
- Общие сведения. [Мониторинг Key Vault с помощью службы "Сетка событий Azure" (предварительная версия)](../general/event-grid-overview.md)
- Руководство. [Создание первой функции на портале Azure](../../azure-functions/functions-create-first-azure-function.md)
- Руководство. [Получение сообщения электронной почты при изменении секрета в хранилище ключей](../general/event-grid-logicapps.md)
- [Схема событий службы "Сетка событий Azure" для Azure Key Vault (предварительная версия)](../../event-grid/event-schema-key-vault.md)
