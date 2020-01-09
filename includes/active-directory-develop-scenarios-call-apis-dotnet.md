---
title: включить файл
description: включить файл
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
ms.openlocfilehash: 4e01dbb0036761215a9a05c464b20ead340a2e3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423763"
---
### <a name="authenticationresult-properties-in-msalnet"></a>Свойства AuthenticationResult в MSAL.NET

Методы получения маркеров возвращают `AuthenticationResult` (или для асинхронных методов — `Task<AuthenticationResult>`.

В MSAL.NET `AuthenticationResult` предоставляет:

- `AccessToken` для доступа веб-API к ресурсам. Этот параметр представляет собой строку, обычно JWT в кодировке Base64, но клиент никогда не должен искать в маркере доступа. Стабильность формата не гарантируется, и маркер может быть зашифрован для конкретного ресурса. Люди, код которых зависит от содержимого маркера доступа на стороне клиента, являются одним из основных источников ошибок и разрывов логики клиента. См. также [маркеры доступа](../articles/active-directory/develop/access-tokens.md)
- `IdToken` для пользователя (этот параметр является закодированным JWT). См. [маркеры идентификации](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` сообщает дату и время истечения срока действия маркера
- `TenantId` содержит клиент, в котором был найден пользователь. Для гостевых пользователей (сценарии Azure AD B2B) идентификатор клиента является гостевым клиентом, а не уникальным клиентом.
При доставке маркера для пользователя `AuthenticationResult` также содержит сведения об этом пользователе. Для конфиденциальных потоков клиента, где маркеры запрашиваются без пользователя (для приложения), эти сведения о пользователе имеют значение null.
- `Scopes`, для которого был выдан маркер.
- Уникальный идентификатор пользователя.

### <a name="iaccount"></a>иаккаунт

MSAL.NET определяет понятие учетной записи (через интерфейс `IAccount`). Это важное изменение предоставляет правильную семантику, то есть отражает возможность использования одним пользователем нескольких учетных записей в разных каталогах AAD. Кроме того, MSAL.NET предоставляет лучшую информацию в случае гостевых сценариев, так как предоставляются сведения о домашней учетной записи.
На следующей схеме показана структура интерфейса `IAccount`.

![изображение](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

Класс `AccountId` определяет учетную запись в определенном клиенте. Имеет следующие свойства.

| Свойство | Description |
|----------|-------------|
| `TenantId` | Строковое представление идентификатора GUID, которое является ИДЕНТИФИКАТОРом клиента, где находится учетная запись. |
| `ObjectId` | Строковое представление идентификатора GUID, которое является ИДЕНТИФИКАТОРом пользователя, владеющего учетной записью в клиенте. |
| `Identifier` | Уникальный идентификатор учетной записи. `Identifier` является объединением `ObjectId` и `TenantId` разделенными запятыми и не кодируется в кодировке Base64. |

Интерфейс `IAccount` представляет сведения об одной учетной записи. Один и тот же пользователь может присутствовать в разных клиентах, то есть у пользователя может быть несколько учетных записей. Его члены:

| Свойство | Description |
|----------|-------------|
| `Username` | Строка, содержащая отображаемое значение в формате UserPrincipalName (UPN), например john.doe@contoso.com. Эта строка может иметь значение null, тогда как Хомеаккаунтид и Хомеаккаунтид. Identifier не будут иметь значение null. Это свойство заменяет свойство `DisplayableId` `IUser` в предыдущих версиях MSAL.NET. |
| `Environment` | Строка, содержащая поставщик удостоверений для этой учетной записи, например `login.microsoftonline.com`. Это свойство заменяет свойство `IdentityProvider` `IUser`, за исключением того, что `IdentityProvider` также содержит сведения о клиенте (в дополнение к облачной среде), в то время как это значение является только узлом. |
| `HomeAccountId` | AccountId учетной записи домашнего пользователя. Это свойство уникально идентифицирует пользователя в клиентах Azure AD. |

### <a name="using-the-token-to-call-a-protected-api"></a>Использование токена для вызова защищенного API

После того как `AuthenticationResult` возвращено MSAL (в `result`), необходимо добавить его в заголовок авторизации HTTP перед вызовом доступа к защищенному веб-API.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```