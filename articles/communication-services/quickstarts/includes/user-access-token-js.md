---
title: включить файл
description: включить файл
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 77b1e9ab245f668ab81741451a5e032f37bc3625
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945756"
---
## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
- [Node.js](https://nodejs.org/), активная версия LTS и версия Maintenance LTS (рекомендуются версии 8.11.1 и 10.14.1).
- Активный ресурс Служб коммуникации и строка подключения. [Создайте ресурс Служб коммуникации.](../create-communication-resource.md)

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-nodejs-application"></a>Создание нового приложения Node.js

Откройте терминал или командное окно, создайте каталог для своего приложения и перейдите к нему.

```console
mkdir user-tokens-quickstart && cd user-tokens-quickstart
```

Воспользуйтесь командой `npm init -y`, чтобы создать файл **package.json** с параметрами по умолчанию.

```console
npm init -y
```

### <a name="install-the-package"></a>Установка пакета

Выполните команду `npm install`, чтобы установить клиентскую библиотеку администрирования Служб коммуникации для JavaScript.

```console

npm install @azure/communication-administration --save

```

Параметр `--save` указывает библиотеку как зависимость в файле пакета **package.json**.

## <a name="set-up-the-app-framework"></a>Настройка платформы приложения

Из каталога проекта:

1. Откройте новый текстовый файл в редакторе кода.
1. Добавьте вызов `require`, чтобы загрузить `CommunicationIdentityClient`.
1. Создайте структуру программы, включая простую обработку исключений.

Используйте следующий код:

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');

const main = async () => {
  console.log("Azure Communication Services - User Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. Сохраните новый файл как **issue-token.js** в каталоге *user-tokens-quickstart*.

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

Создайте экземпляр `CommunicationIdentityClient` с использованием строки подключения. Приведенный ниже код извлекает строку подключения для ресурса из переменной среды с именем `COMMUNICATION_SERVICES_CONNECTION_STRING`. См. сведения о том, как [управлять строкой подключения ресурса](../create-communication-resource.md#store-your-connection-string).

Добавьте следующий код в метод `main`:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the user token client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-a-user"></a>Создание пользователя

Службы коммуникации Azure позволяют использовать упрощенный каталог удостоверений. Чтобы создать новую запись в каталоге с уникальным идентификатором (`Id`), используйте метод `createUser`. Необходимо обеспечить сопоставление между пользователями приложения и удостоверениями, созданными Службами коммуникации (например, храня удостоверения в базе данных сервера приложений).

```javascript
let userResponse = await identityClient.createUser();
console.log(`\nCreated a user with ID: ${userResponse.communicationUserId}`);
```

## <a name="issue-user-access-tokens"></a>Выдача маркеров доступа пользователей

Чтобы выдать маркер доступа для пользователя Служб коммуникации, используйте метод `issueToken`. Если не указать необязательный параметр `user`, новый пользователь будет создан и возвращен вместе с маркером.

```javascript
// Issue an access token with the "voip" scope for a new user
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued a token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

Маркеры доступа пользователей — это кратковременные учетные данные, которые необходимо повторно выдавать, чтобы предотвратить сбои в работе служб. Свойство ответа `expiresOn` указывает на время существования маркера.

## <a name="revoke-user-access-tokens"></a>Отзыв маркеров доступа пользователей

Иногда может потребоваться явно отозвать маркеры доступа пользователя, например когда пользователь изменяет пароль, используемый для проверки подлинности в службе. При этом используется метод `revokeTokens`, чтобы сделать недействительными все маркеры доступа пользователя.

```javascript  
await identityClient.revokeTokens(userResponse);
console.log(`\nSuccessfully revoked all tokens for user with Id: ${userResponse.communicationUserId}`);
```

## <a name="delete-a-user"></a>Удаление пользователя

При удалении пользователя отзываются все активные маркеры и запрещается выдача последующих маркеров для удостоверений. Вместе с этим удаляется и все хранимое содержимое, связанное с пользователем.

```javascript
await identityClient.deleteUser(userResponse);
console.log(`\nDeleted the user with Id: ${userResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>Выполнение кода

В окне консоли перейдите в каталог, содержащий файл *issue-token.py*, а затем выполните команду `node`, чтобы запустить приложение.

```console
node ./issue-token.js
```
