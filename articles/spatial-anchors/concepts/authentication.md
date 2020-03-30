---
title: Аутентификация и авторизация
description: Узнайте о различных способах проверки подлинности приложения или службы на пространственных якорях Azure и уровнях контроля, которые необходимо получить доступ к пространственным якорям Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: df27a77c202951a6c789703f12712e75bd8b5906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656995"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Аутентификация и авторизация пространственных якорей Azure

В этом разделе мы рассмотрим различные способы проверки подлинности пространственных якорей Azure из вашего приложения или веб-сервиса, а также способы использования управления доступом на основе ролей в каталоге Azure (Azure AD) для управления доступом к учетным записям spatial Anchors.

## <a name="overview"></a>Обзор

![Обзор аутентификации для пространственных якорей Azure](./media/spatial-anchors-authentication-overview.png)

Чтобы получить доступ к данной учетной записи Azure Spatial Anchors, клиентам необходимо сначала получить токен доступа от службы доступа Для доступа Azure Mixed Reality Security Service (STS). Токены, полученные от STS, живут в течение 24 часов и содержат информацию для служб пространственных якорей для принятия решений о авторизации по счету, а также гарантируют, что только уполномоченные директора могут получить доступ к этой учетной записи.

Токены доступа могут быть получены в обмен на ключи учетной записи или с токенов, выпущенных Azure AD.

Ключи учетной записи позволяют быстро приступить к использованию службы пространственных якорей Azure; Однако перед развертыванием приложения в производство рекомендуется обновить приложение для использования аутентификации на основе Azure AD.

Токены аутентификации Azure AD можно получить двумя способами:

- Если вы строите корпоративное приложение, и ваша компания использует Azure AD в качестве системы идентификации, вы можете использовать пользовательскую проверку Azure AD в приложении и предоставить доступ к учетным записям пространственных якорей с помощью существующих групп безопасности Azure AD или непосредственно пользователям вашей организации.
- В противном случае рекомендуется получать токены Azure AD из веб-службы, поддерживающей ваше приложение. Использование поддерживающего веб-сервиса является рекомендуемым методом проверки подлинности для производственных приложений, поскольку он позволяет избежать встраивания учетных данных для доступа к пространственным якорям Azure в клиентском приложении.

## <a name="account-keys"></a>Ключи от счета

Использование ключей учетной записи для доступа к вашей учетной записи Azure Пространственные якоря является самым простым способом, чтобы начать работу. Ключи учетной записи вы найдете на портале Azure. Перейдите к учетной записи и выберите вкладку "Ключи".

![Обзор аутентификации для пространственных якорей Azure](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Предоставляются два ключа, которые одновременно действительны для доступа к учетной записи Пространственных якорей. Рекомендуется регулярно обновлять ключ, который используется для доступа к учетной записи; наличие двух отдельных действительных ключей позволяет такие обновления без простоя; Вы только должны обновить альтернативно основной ключ и вторичный ключ.

SDK имеет встроенную поддержку для аутентификации с ключами учетной записи; Вам просто необходимо установить свойство AccountKey на объекте cloudSession.

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="c-ndk"></a>[СЕ НДК](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[СЕ Винрт](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Как только это будет сделано, SDK будет обрабатывать обмен ключом учетной записи для токена доступа и необходимым кэшированием токенов для вашего приложения.

> [!WARNING]
> Использование ключей учетной записи рекомендуется для быстрой посадки на борт, но только при разработке/прототипировании. Настоятельно рекомендуется не отстранять приложение в производство с помощью встроенного ключа учетной записи, а вместо этого использовать перечисленные далее подходы проверки подлинности Azure AD на основе пользовательских или сервисных служб.

## <a name="azure-ad-user-authentication"></a>Проверка подлинности пользователей Azure AD

Для приложений, ориентированных на пользователей Active Directory Azure, рекомендуется использовать маркер Azure AD для пользователя, который можно получить с помощью [библиотеки MSAL.](../../active-directory/develop/msal-overview.md) Вы должны следовать шагам, перечисленным [в регистре, быстрому запуску приложения,](../../active-directory/develop/quickstart-register-app.md)которые включают в себя:

1. Конфигурация на портале Azure
    1.  Зарегистрируйте свое заявление в Azure AD в качестве **native-приложения.** В рамках регистрации необходимо определить, должно ли ваше приложение быть мультитенантным или нет, и предоставить реналорные URL-адреса, разрешенные для вашего приложения.
        1.  Переключиться на вкладку **разрешений API**
        2.  Выберите **Добавить разрешение**
            1.  Выберите **поставщика ресурсов смешанной реальности** под **A- ИП, который использует моя организация**
            2.  Выберите **Делегированные разрешения**
            3.  Проверьте окно для **mixedreality.signin** под **смешанной реальностью**
            4.  Выберите **разрешения добавления**
        3.  Выберите **согласие админа Гранта**
    2.  Предоставьте вашему приложению или пользователям доступ к вашему ресурсу:
        1.  Перейдите на ресурс spatial Anchors на портале Azure
        2.  Переключиться на вкладку **управления доступом (IAM)**
        3.  Назначение роли Хит **Добавить**
            1.  [Выберите роль](#role-based-access-control)
            2.  В поле **Select** введите имя пользователя (ы), группы (ы) и/или приложения (ы), к которому требуется присвоить доступ.
            3.  Щелкните **Сохранить**.
2. В коде:
    1.  Убедитесь в том, чтобы использовать **идентификатор приложения** и **перенаправить Uri** собственного приложения Azure AD в качестве **идентификатора клиента** и **перенаправить uri** параметров в ADAL
    2.  Установите информацию о арендаторе:
        1.  Если приложение поддерживает **только мою организацию,** замените это значение **идентификатором арендатора** или **именем арендатора** (например, contoso.microsoft.com)
        2.  Если приложение поддерживает **учетные записи в любом каталоге организации,** замените это значение **организациями**
        3.  Если приложение поддерживает **всех пользователей учетной записи Майкрософт,** замените это значение **общим**
    3.  На вашем запросе маркера установите **ресурс** на ".https://sts.mixedreality.azure.com Этот "ресурс" укажет AD Azure, что приложение запрашивает маркер для службы пространственных якорей Azure.

При этом приложение должно иметь возможность получить от MSAL токен Azure AD; токен Azure AD можно установить в качестве **маркера проверки подлинности** на объекте конфигурации сеанса облака.

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="c-ndk"></a>[СЕ НДК](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[СЕ Винрт](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Аутентификация службы AD Azure

Рекомендуемая опция для развертывания приложений, использующих пространственные якоря Azure, заключается в использовании бэкэнд-сервиса, который будет брокером запросов на аутентификацию. Общая схема должна быть описана на этой диаграмме:

![Обзор аутентификации для пространственных якорей Azure](./media/spatial-anchors-aad-authentication.png)

Здесь предполагается, что ваше приложение использует свой собственный механизм (например, учетная запись Microsoft, PlayFab, Facebook, Google ID, пользовательское имя/пароль и т.д.) для проверки подлинности службы бэкэнда. После проверки подлинности пользователей в службе бэкэнда эта служба может получить токен Azure AD, обменять его на кекс доступа для пространственных якорей Azure и вернуть его обратно в клиентское приложение.

Токен доступа Azure AD извлекается с помощью [библиотеки MSAL.](../../active-directory/develop/msal-overview.md) Вы должны следовать шагам, перечисленным [в регистре, быстрому запуску приложения,](../../active-directory/develop/quickstart-register-app.md)которые включают в себя:

1.  Конфигурация на портале Azure:
    1.  Зарегистрируйте свое заявление в Azure AD:
        1.  На портале Azure перейдите в **Active Directory Azure**и выберите **регистрацию приложений**
        2.  Выберите **новую регистрацию заявок**
        3.  Введите название приложения, выберите **Web-приложение / API** в качестве типа приложения и введите URL-адрес auth для вашего сервиса. Затем нажмите **Создать**.
        4.  На этом приложении, **нажмите Настройки**, а затем выберите **вкладку Ключей.** Введите имя вашего ключа, выберите продолжительность, и нажмите **Сохранить**. Убедитесь в том, чтобы сохранить ключевое значение, которое отображается в это время, так как вам нужно будет включить его в код веб-службы.
    2.  Предоставьте приложению и/или пользователям доступ к вашему ресурсу:
        1.  Перейдите на ресурс spatial Anchors на портале Azure
        2.  Переключиться на вкладку **управления доступом (IAM)**
        3.  Назначение роли Хит **Добавить**
        1.  [Выберите роль](#role-based-access-control)
        2.  В **выбранном** поле введите созданное приложение (ы), к которому вы хотите назначить доступ. Если вы хотите, чтобы пользователи приложения имели различные роли против учетной записи Spatial Anchors, необходимо зарегистрировать несколько приложений в Azure AD и назначить каждой отдельной роли. Затем реализуйте логику авторизации, чтобы использовать правильную роль для пользователей.
    3.  Щелкните **Сохранить**.
2.  В коде (обратите внимание: вы можете использовать образец сервиса, включенный на GitHub):
    1.  Убедитесь в том, чтобы использовать идентификатор приложения, секрет приложения и перенаправить Uri собственного приложения Azure AD в качестве идентификатора клиента, секрета и перенаправления параметров ADAL
    2.  Установите идентификатор клиента на свой собственный идентификатор клиента AAAzure ADD в параметре органа в ADAL
    3.  На вашем запросе маркера,https://sts.mixedreality.azure.comустановите **ресурс** на "

При этом служба бэкэнда может получить токен Azure AD. Затем он может обменять его на токен MR, который вернется к клиенту. Использование маркера Azure AD для получения токена MR выполняется с помощью вызова REST. Вот пример вызова:

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

В тех случаях, когда заголовок авторизации отформатирован следующим образом:`Bearer <accoundId>:<accountKey>`

И ответ содержит токен MR в простом тексте.

Токен MR затем возвращается клиенту. Приложение клиента может установить его в качестве маркера доступа в конфигурации сеанса облака.

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="c-ndk"></a>[СЕ НДК](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[СЕ Винрт](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Управление доступом на основе ролей

Чтобы контролировать уровень доступа к приложениям, службам или пользователям Azure AD вашего сервиса, были созданы следующие роли, которые вы могли назначить по мере необходимости учетным записям пространственных якорей Azure:

- **Владелец учетной записи spatial Anchors:** приложения или пользователи, которые имеют эту роль, могут создавать пространственные якоря, запросить их и удалить их. При проверке подлинности учетной записи с помощью ключей учетной записи роль **владельца учетной записи Spatial Anchors** назначается проверенному принципу.
- **Автор учетной записи Spatial Anchors**: приложения или пользователи, которые имеют эту роль, могут создавать пространственные якоря, запросить их, но не могут удалить их.
- **Spatial Anchors Account Reader**: приложения или пользователи, которые имеют эту роль, могут запросить только пространственные якоря, но не могут создавать новые, удалять существующие или обновлять метаданные на пространственных якорях. Это обычно используется для приложений, где некоторые пользователи курируют среду, в то время как другие могут только вспомнить якоря, ранее размещенные в этой среде.

## <a name="next-steps"></a>Дальнейшие действия

Создайте свое первое приложение с помощью пространственных якорей Azure.

> [!div class="nextstepaction"]
> [Единство (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Единство (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Единство (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Ксамарин (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Ксамарин (iOS)](../quickstarts/get-started-xamarin-ios.md)
