---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 0196d39f5b131bc54e00412beb7fdf10b7352336
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075150"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Свойства AuthenticationResult в MSAL.NET

Методы для получения маркеров, возвращают `AuthenticationResult` (или, для асинхронных методов, `Task<AuthenticationResult>`.

В MSAL.NET `AuthenticationResult` предоставляет:

- `AccessToken` для веб-API для доступа к ресурсам. Этот параметр представляет собой строку, обычно в JWT в кодировке base64, но клиент никогда не следует заглянуть внутрь маркер доступа. Формат не обязательно будет оставаться неизменной, и их можно шифровать для ресурса. Люди написание кода в зависимости от содержимого токена доступа на стороне клиента является одним из основных источников ошибок и разрывов логики клиента. См. также [маркеры доступа](../articles/active-directory/develop/access-tokens.md)
- `IdToken` для пользователя (этот параметр является кодировке JWT). См. в разделе [маркеры Идентификации](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` Указывает дату и время, когда истекает срок действия маркера
- `TenantId` содержит клиент, в котором пользователь был найден. Для гостевых пользователей (Azure AD B2B сценарии) идентификатор клиента — клиент гостя, не уникальный клиента.
При доставке маркер для пользователя, `AuthenticationResult` также содержит сведения об этом пользователя. Для потоков конфиденциального клиента, где токены запрашиваются без пользователя (для приложения) эта информация пользователя имеет значение null.
- `Scopes` Для которого был выдан маркер.
- Уникальный идентификатор для пользователя.

### <a name="iaccount"></a>IAccount

MSAL.NET определяет понятие учетной записи (через `IAccount` интерфейс). Это критическое изменение предоставляет семантику справа: тот факт, что тот же пользователь может иметь несколько учетных записей в Azure разных каталогов AD. Также MSAL.NET предоставляет более подробные сведения в случае гостевой сценариев, как информация домашней учетной записи.
На следующей схеме показана структура `IAccount` интерфейса:

![image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId` Определяет учетную запись в конкретном клиенте. Имеет следующие свойства.

| Свойство | ОПИСАНИЕ |
|----------|-------------|
| `TenantId` | Строковое представление идентификатора GUID, который является Идентификатором клиента, где находится учетная запись. |
| `ObjectId` | Строковое представление идентификатора GUID, который является идентификатор пользователя, которому принадлежит учетная запись в клиенте. |
| `Identifier` | Уникальный идентификатор для учетной записи. `Identifier` — это объединение `ObjectId` и `TenantId` разделенных запятой и находятся не в кодировке base64. |

`IAccount` Интерфейс представляет сведения об одной учетной записи. Одного пользователя могут быть представлены в разных клиентах, то есть у пользователя может быть несколько учетных записей. Его члены являются:

| Свойство | ОПИСАНИЕ |
|----------|-------------|
| `Username` | Строка, содержащая отображаемое значение в формате UserPrincipalName (UPN), например, john.doe@contoso.com. Эта строка может быть равно null, тогда как HomeAccountId и HomeAccountId.Identifier не будет иметь значение null. Это свойство заменяет собой `DisplayableId` свойство `IUser` в предыдущей версии MSAL.NET. |
| `Environment` | Строка, содержащая поставщик удостоверений для этой учетной записи, например, `login.microsoftonline.com`. Это свойство заменяет собой `IdentityProvider` свойство `IUser`, за исключением того, что `IdentityProvider` было сведения о клиенте (в дополнение к облачной среде), а значение Вот только узла. |
| `HomeAccountId` | AccountId домашней учетной записи для пользователя. Это свойство уникально идентифицирует пользователя всех клиентов Azure AD. |

### <a name="using-the-token-to-call-a-protected-api"></a>С помощью токена для вызова через защищенный API

Один раз `AuthenticationResult` возвращена MSAL (в `result`), необходимо добавить его в заголовок авторизации HTTP, перед вызовом для доступа к защищенным веб-API.

```CSharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```