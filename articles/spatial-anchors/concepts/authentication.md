---
title: Аутентификация и авторизация
description: В этой статье описываются различные способы аутентификации в службе "Пространственные привязки Azure" из приложений и служб, а также уровни контроля, которые требуются для управления доступом к службе "Пространственные привязки Azure".
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: baf5252a6b158855739546c2a03e63dceee6701e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84456510"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Аутентификация и авторизация в службе "Пространственные привязки Azure"

В этом разделе мы рассмотрим различные способы аутентификации в службе "Пространственные привязки Azure" из приложений и веб-служб. Здесь также рассматриваются способы применения такого механизма Azure Active Directory (AAD), как управление доступом на основе ролей, для управления доступом к учетным записям Пространственных привязок.

## <a name="overview"></a>Обзор

![Обзор аутентификации в службе "Пространственные привязки Azure"](./media/spatial-anchors-authentication-overview.png)

Чтобы получить доступ к конкретной учетной записи службы "Пространственные привязки Azure", клиенту необходимо сначала получить маркер доступа от службы токенов безопасности для Смешанной реальности Azure. Маркеры доступа, полученные от службы токенов безопасности, можно использовать в течение 24 часов. Они содержат сведения для служб Пространственных привязок, позволяющие принимать решения об авторизации для учетной записи и предоставлять доступ к ней только авторизованным субъектам.

Маркеры доступа можно получить в обмен на ключи учетной записи или на токены, выпущенные службой AAD.

Ключи учетной записи позволяют быстро начать работу со службой "Пространственные привязки Azure". Однако перед развертыванием приложения в рабочей среде рекомендуем обновить его, чтобы реализовать возможность аутентификации на основе AAD.

Токены AAD для аутентификации можно получить двумя способами:

- Если вы создаете корпоративное приложение и ваша компания использует AAD в качестве системы идентификации, вы можете применять в приложении аутентификацию AAD на уровне пользователей и предоставлять доступ к учетным записям пространственных привязок существующим группам безопасности AAD или непосредственно пользователям в организации.
- В противном случае рекомендуем получать токены AAD из веб-службы, поддерживающей приложение. Использование поддерживающей веб-службы — рекомендуемый метод аутентификации для приложений в рабочей среде, так как он позволяет избежать внедрения учетных данных для доступа к службе "Пространственные привязки Azure" в клиентское приложение.

## <a name="account-keys"></a>Ключи учетной записи

Чтобы быстро приступить к работе с Пространственными привязками Azure, проще всего воспользоваться ключами учетной записи. Их можно найти на портале Microsoft Azure. Перейдите к своей учетной записи и откройте вкладку "Ключи".

![Обзор аутентификации в службе "Пространственные привязки Azure"](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

Доступны два ключа, и для доступа к учетной записи Пространственных привязок можно использовать любой из них. Рекомендуем регулярно обновлять ключ, используемый для доступа к учетной записи. Поскольку есть два действительных ключа: первичный и вторичный, — их можно обновлять по очереди, не вызывая простоев.

В пакете SDK есть встроенная поддержка аутентификации с помощью ключей учетной записи. Вам нужно только задать свойство AccountKey для объекта cloudSession.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

После этого пакет SDK произведет обмен ключа учетной записи на маркер доступа и необходимое кэширование маркеров для приложения.

> [!WARNING]
> Использование ключей учетной записи рекомендуется для быстрого начала работы, но только на этапе разработки или создания прототипа. Настоятельно рекомендуем не переносить приложение в рабочую среду с помощью внедренного ключа учетной записи. Вместо этого следует использовать описанные ниже способы аутентификации с помощью AAD на уровне пользователей или служб.

## <a name="azure-ad-user-authentication"></a>Аутентификация пользователей с помощью AAD

Если приложение предназначено для пользователей Azure Active Directory, рекомендуем применять токен AAD для пользователя, который можно получить с помощью [библиотеки MSAL](../../active-directory/develop/msal-overview.md). Выполните действия, описанные в [кратком руководстве по регистрации приложения](../../active-directory/develop/quickstart-register-app.md). Они перечислены ниже.

1. Настройка на портале Microsoft Azure.
    1.  Зарегистрируйте приложение в AAD в качестве **собственного приложения**. В процессе регистрации необходимо определить, должно ли приложение быть мультитенантным, и предоставить URL-адреса перенаправления, разрешенные для приложения.
        1.  Перейдите на вкладку **Разрешения API**.
        2.  Нажмите **Добавить разрешение**.
            1.  На вкладке **Интерфейсы API, используемые моей организацией** выберите **Microsoft Mixed Reality**.
            2.  Нажмите **Делегированные разрешения**.
            3.  Установите флажок для **mixedreality.signin** в разделе **mixedreality**.
            4.  Нажмите кнопку **Добавить разрешения**.
        3.  Нажмите **Предоставить согласие администратора**.
    2.  Предоставьте приложению или пользователям доступ к вашему ресурсу:
        1.  На портале Microsoft Azure перейдите к своему ресурсу Пространственных привязок.
        2.  Откройте вкладку **Управление доступом (IAM)** .
        3.  Щелкните **Добавить назначение ролей**.
            1.  [Выберите роль](#role-based-access-control).
            2.  В поле **Выберите** введите имена пользователей, групп и (или) приложений, которым вы хотите назначить доступ.
            3.  Щелкните **Сохранить**.
2. В своем коде выполните указанные ниже действия.
    1.  Убедитесь, что в MSAL в качестве параметров **clientId** и **RedirectUri** используются **ИД** и **URI перенаправления** вашего приложения AAD.
    2.  Введите сведения об арендаторе.
        1.  Если ваше приложение поддерживает вариант **Только моя организация**, замените это значение на **ИД клиента** или **имя клиента** (например, contoso.microsoft.com).
        2.  Если приложение поддерживает вариант **учетные записи в любом каталоге организации**, замените это значение на **Организации**.
        3.  Если приложение поддерживает вариант **Все пользователи с учетными записями Майкрософт**, замените это значение на **Общее**.
    3.  В запросе токена задайте для параметра **scope** значение "https://sts.mixedreality.azure.com//.default". Этот параметр будет указывать на службу AAD, у которой приложение запрашивает маркер для службы токенов безопасности для Смешанной реальности.

В результате приложение должно иметь возможность получить из MSAL токен AAD. Вы можете задать этот токен в качестве значения **authenticationToken** в объекте конфигурации облачного сеанса.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Аутентификация с помощью AAD на уровне служб

Развертывать приложения, использующие службу "Пространственные привязки Azure", в рабочей среде рекомендуется с помощью внутренней службы, которая будет выступать брокером для запросов на аутентификацию. Общая схема выглядит так:

![Обзор аутентификации в службе "Пространственные привязки Azure"](./media/spatial-anchors-aad-authentication.png)

В этом случае предполагается, что ваше приложение использует собственный механизм (например, учетную запись Майкрософт, PlayFab, Facebook, Google ID, настраиваемое имя пользователя и пароль и т. д.) для аутентификации в своей внутренней службе. После аутентификации пользователей во внутренней службе она может получить токен AAD, обменять его на маркер доступа для службы "Пространственные привязки Azure" и вернуть этот маркер в клиентское приложение.

Токен AAD извлекается с помощью[библиотеки MSAL](../../active-directory/develop/msal-overview.md). Выполните действия, описанные в [кратком руководстве по регистрации приложения](../../active-directory/develop/quickstart-register-app.md). Они перечислены ниже.

1.  Настройка на портале Microsoft Azure.
    1.  Зарегистрируйте приложение в AAD.
        1.  На портале Microsoft Azure перейдите в раздел **Azure Active Directory**, а затем выберите **Регистрация приложений**.
        2.  Выберите **Регистрация нового приложения**.
        3.  Введите имя приложения, выберите для него тип **Веб-приложение или API** и введите URL-адрес аутентификации для своей службы. Затем нажмите **Создать**.
        4.  В этом приложении нажмите **Параметры**, а затем выберите вкладку **Сертификаты и секреты** . Создайте новый секрет клиента, выберите длительность и нажмите кнопку **добавить**. Не забудьте сохранить значение секрета, так как его необходимо включить в код веб-службы.
    2.  Предоставьте приложению и (или) пользователям доступ к своему ресурсу.
        1.  На портале Microsoft Azure перейдите к своему ресурсу Пространственных привязок.
        2.  Откройте вкладку **Управление доступом (IAM)** .
        3.  Щелкните **Добавить назначение ролей**.
        1.  [Выберите роль](#role-based-access-control).
        2.  В поле **Выберите** введите имена созданных приложений, которым вы хотите назначить доступ. Если вы хотите, чтобы пользователи приложения могли получать разные роли для учетной записи Пространственных привязок, зарегистрируйте в AAD несколько приложений и назначьте каждому из них отдельную роль. Затем реализуйте логику авторизации, чтобы пользователи получали нужные роли.
        3.  Примечание. в области **добавить назначение ролей** необходимо задать для параметра **доступ** к параметру значение "пользователь Azure AD, группа или субъект-служба".
    3.  Щелкните **Сохранить**.
2.  В своем коде выполните указанные ниже действия (примечание: вы можете использовать пример службы из GitHub).
    1.  Убедитесь, что в MSAL в качестве параметров clientId, secret и RedirectUri используются ИД, секрет и URI перенаправления вашего приложения AAD.
    2.  В параметре authority в MSAL задайте в качестве ИД клиента собственный ИД клиента AAD.
    3.  В запросе токена задайте для параметра **scope** значение "https://sts.mixedreality.azure.com//.default".

В результате внутренняя служба сможет получить токен AAD. Затем она обменяет его на токен СР, который будет возвращен клиенту. Для получения токена СР в обмен на токен AAD используется вызов REST. Вот пример такого вызова:

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Здесь заголовок авторизации имеет следующий формат: `Bearer <Azure_AD_token>`.

Ответ содержит токен СР в виде обычного текста.

Этот токен СР возвращается клиенту. Затем клиентское приложение может задать его в качестве маркера доступа в конфигурации облачного сеанса.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Управление доступом на основе ролей

Для управления уровнем доступа, предоставляемого приложениям, службам или пользователям из AAD, созданы указанные ниже роли. Вы можете назначать их для использования учетных записей службы "Пространственные привязки Azure".

- **Владелец учетной записи пространственных привязок**. Приложения и пользователи с этой ролью могут создавать, запрашивать и удалять пространственные привязки. Роль **Владелец учетной записи пространственных привязок** назначается субъекту, прошедшему аутентификацию с использованием ключей учетной записи.
- **Участник учетной записи пространственных привязок**. Приложения и пользователи с этой ролью могут создавать и запрашивать пространственные привязки, но не могут их удалять.
- **Читатель учетной записи пространственных привязок**. Приложения и пользователи с этой ролью могут только запрашивать пространственные привязки, но не могут создавать и удалять их, а также обновлять метаданные на пространственных привязках. Эта роль обычно используется для приложений, в которых одни пользователи курируют среду, а другие могут только повторно вызывать привязки, которые уже помещены в эту среду.

## <a name="next-steps"></a>Дальнейшие действия

Создайте свое первое приложение с помощью службы "Пространственные привязки Azure".

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
