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
ms.openlocfilehash: 5c9066f369183de3b4cfe19cc5635e8f1b4a94a2
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779434"
---
## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно. 
- Последняя версия [клиентской библиотеки NET Core](https://dotnet.microsoft.com/download/dotnet-core) для вашей операционной системы.
- Активный ресурс Служб коммуникации и строка подключения. [Создайте ресурс Служб коммуникации.](../create-communication-resource.md)

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-c-application"></a>Создание нового приложения C#

В окне консоли (cmd, PowerShell или Bash) выполните команду `dotnet new`, чтобы создать консольное приложение с именем `UserAccessTokensQuickstart`. Эта команда создает простой проект Hello World на языке C# с одним файлом исходного кода: **Program.cs**.

```console
dotnet new console -o UserAccessTokensQuickstart
```

Измените каталог на только что созданную папку приложения и выполните команду `dotnet build`, чтобы скомпилировать приложение.

```console
cd UserAccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Установка пакета

Оставаясь в каталоге приложения, установите пакет библиотеки администрирования Служб коммуникации для .NET с помощью команды `dotnet add package`.

```console
dotnet add package Azure.Communication.Administration --version 1.0.0-beta.2
```

### <a name="set-up-the-app-framework"></a>Настройка платформы приложения

Из каталога проекта:

1. Откройте файл **Program.cs** в текстовом редакторе.
1. Добавьте директиву `using` для включения пространства имен `Azure.Communication.Administration`.
1. Обновите объявление метода `Main` для поддержки асинхронного кода.

Используйте следующий код:

```csharp
using System;
using Azure.Communication.Administration;

namespace UserAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - User Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

Инициализируйте экземпляр `CommunicationIdentityClient` с использованием строки подключения. Приведенный ниже код извлекает строку подключения для ресурса из переменной среды с именем `COMMUNICATION_SERVICES_CONNECTION_STRING`. См. сведения о том, как [управлять строкой подключения ресурса](../create-communication-resource.md#store-your-connection-string).

Добавьте следующий код в метод `Main`:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string ConnectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(ConnectionString);
```

## <a name="create-a-user"></a>Создание пользователя

Службы коммуникации Azure позволяют использовать упрощенный каталог удостоверений. Чтобы создать новую запись в каталоге с уникальным идентификатором (`Id`), используйте метод `createUser`. Необходимо обеспечить сопоставление между пользователями приложения и удостоверениями, созданными Службами коммуникации (например, храня удостоверения в базе данных сервера приложений).

```csharp
var userResponse = await client.CreateUserAsync();
var user = userResponse.Value;
Console.WriteLine($"\nCreated a user with ID: {user.Id}");
```

## <a name="issue-user-access-tokens"></a>Выдача маркеров доступа пользователей

Чтобы выдать маркер доступа для пользователя Служб коммуникации, используйте метод `issueToken`. Если не указать необязательный параметр `user`, новый пользователь будет создан и возвращен вместе с маркером.

```csharp
// Issue an access token with the "voip" scope for a new user
var tokenResponse = await client.IssueTokenAsync(user, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued a token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

Маркеры доступа пользователей — это кратковременные учетные данные, которые необходимо повторно выдавать, чтобы предотвратить сбои в работе служб. Свойство ответа `expiresOn` указывает на время существования маркера.

## <a name="revoke-user-access-tokens"></a>Отзыв маркеров доступа пользователей

Иногда может потребоваться явно отозвать маркеры доступа пользователя, например когда пользователь изменяет пароль, используемый для проверки подлинности в службе. Такая возможность реализуется посредством клиентской библиотеки администрирования для Служб коммуникации Azure.

```csharp  
await client.RevokeTokensAsync(user);
Console.WriteLine($"\nSuccessfully revoked all tokens for user with ID: {user.Id}");
```

## <a name="delete-a-user"></a>Удаление пользователя

При удалении удостоверения отзываются все активные маркеры и запрещается выдача последующих маркеров для удостоверений. Вместе с этим удаляется и все хранимое содержимое, связанное с пользователем.

```csharp
await client.DeleteUserAsync(user);
Console.WriteLine($"\nDeleted the user with ID: {user.Id}");
```

## <a name="run-the-code"></a>Выполнение кода

Запустите приложение из каталога приложения с помощью команды `dotnet run`.

```console
dotnet run
```
