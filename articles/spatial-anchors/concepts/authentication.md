---
title: Проверка подлинности и авторизация для пространственных привязок Azure | Документация Майкрософт
description: Узнайте о различных способах проверки подлинности приложения или службы в пространственных привязках Azure, а также об уровнях управления, которым требуется шлюзовый доступ к пространственным точкам привязки Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 850748462f0273f2dfb1522d900ce9f1b2156d2a
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517069"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Проверка подлинности и авторизация для пространственных привязок Azure

В этом разделе мы рассмотрим различные способы проверки подлинности в пространственных привязках Azure из приложения или веб-службы, а также способы использования управления доступом на основе ролей в каталоге Azure (Azure AD) для управления доступом к учетным записям пространственных привязок.  

## <a name="overview"></a>Обзор

![Общие сведения о проверке подлинности для пространственных привязок Azure](./media/spatial-anchors-authentication-overview.png)

Чтобы получить доступ к указанной учетной записи пространственных привязок Azure, клиентам необходимо сначала получить маркер доступа из службы маркеров безопасности (STS) в смешанной реальности Azure. Токены, полученные от STS Live в течение 24 часов, и содержат сведения для служб пространственных привязок, позволяющие принимать решения об авторизации учетной записи и обеспечивать доступ к этой учетной записи только авторизованным субъектам. 

Маркеры доступа можно получить в Exchange из ключей учетной записи или из маркеров, выпущенных Azure AD. 

Ключи учетной записи позволяют быстро начать работу с использованием службы пространственных привязок Azure. Однако перед развертыванием приложения в рабочей среде рекомендуется обновить приложение для использования проверки подлинности на основе Azure AD. 

Маркеры проверки подлинности Azure AD можно получить двумя способами:

- Если вы создаете корпоративное приложение и ваша компания использует Azure AD в качестве системы идентификации, вы можете использовать проверку подлинности Azure AD в приложении и предоставить доступ к учетным записям пространственных привязок с помощью существующих групп безопасности Azure AD или непосредственно пользователям в Организации. 
- В противном случае рекомендуется получить токены Azure AD из веб-службы, поддерживающей приложение. Использование поддерживающей веб-службы является рекомендуемым методом проверки подлинности для рабочих приложений, так как он позволяет избежать внедрения учетных данных для доступа к пространственным привязкам Azure в клиентском приложении. 

## <a name="account-keys"></a>Ключи учетной записи

Использование ключей учетной записи для доступа к учетной записи пространственных привязок Azure — самый простой способ начать работу. Ключи учетной записи можно найти на портал Azure. Перейдите к своей учетной записи и перейдите на вкладку "ключи".

![Общие сведения о проверке подлинности для пространственных привязок Azure](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Доступны два ключа, которые одновременно допустимы для доступа к учетной записи пространственных привязок. Рекомендуется регулярно обновлять ключ, используемый для доступа к учетной записи. наличие двух отдельных допустимых ключей включает такие обновления без простоев. в качестве альтернативы необходимо обновлять только первичный ключ и вторичный ключ. 

Пакет SDK имеет встроенную поддержку проверки подлинности с помощью ключей учетной записи; необходимо просто задать свойство AccountKey для объекта Клаудсессион. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

После этого пакет SDK будет управлять обменом ключами учетной записи для маркера доступа и необходимым кэшированием маркеров для приложения. 

> [!WARNING] 
> Использование ключей учетной записи рекомендуется для быстрой адаптации, но только во время разработки или создания прототипов. Настоятельно рекомендуется не поставлять приложение в рабочую среду с помощью внедренного ключа учетной записи, а вместо этого использовать способы проверки подлинности Azure AD на основе пользователей или служб, перечисленные ниже.

## <a name="azure-ad-user-authentication"></a>Проверка подлинности пользователей Azure AD

Для приложений, предназначенных для Azure Active Directory пользователей, рекомендуемым подходом является использование маркера Azure AD для пользователя, который можно получить с помощью библиотеки ADAL, как описано в следующей документации: [https://docs.microsoft.com/azure/active-directory/develop/v1-overview](../../active-directory/develop/v1-overview.md); выполните приведенные ниже действия. в разделе "быстрые запуски", включая:

1. Конфигурация в портал Azure
    1.  Зарегистрируйте приложение в Azure AD в качестве **собственного приложения**. В процессе регистрации необходимо определить, должно ли приложение работать в нескольких клиентах, и предоставить URL-адреса перенаправления для приложения.  
    2.  Предоставьте приложению или пользователям доступ к Вашему ресурсу: 
        1.  Перейдите к ресурсу пространственных привязок в портал Azure
        2.  Переключение на вкладку **управления доступом (IAM)**
        3.  Нажмите кнопку " **добавить назначение ролей** "
            1.  [Выберите роль](#role-based-access-control)
            2.  В поле **Выбор** введите имена пользователей, групп и (или) приложений, которым вы хотите назначить доступ к ним. 
            3.  Нажмите кнопку **сохранить**.
2. В коде:
    1.  Обязательно используйте **идентификатор приложения** и **URI перенаправления** своего приложения Azure AD в качестве **идентификатора клиента** и параметров **RedirectUri** в ADAL.
    2.  Задайте сведения о клиенте:
        1.  Если приложение поддерживает **только мою организацию**, замените это значение **идентификатором клиента** или **именем клиента** (например, contoso.Microsoft.com).
        2.  Если приложение поддерживает **учетные записи в любом каталоге Организации**, замените это значение **организациями** .
        3.  Если приложение поддерживает **все учетная запись Майкрософт пользователей**, замените это значение на **Common** .
    3.  В запросе маркера задайте ресурсу значение "https://sts.mixedreality.azure.com". Этот "ресурс" указывает Azure AD на то, что приложение запрашивает маркер для службы пространственных привязок Azure.  

В результате приложение должно иметь возможность получить из ADAL маркера Azure AD. Вы можете задать маркер Azure AD в качестве **authenticationToken** для объекта конфигурации сеанса облака. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Проверка подлинности службы Azure AD

Рекомендуемый вариант развертывания приложений, использующих пространственные привязки Azure в рабочей среде, — использовать серверную службу, которая будет выполнять запросы на проверку подлинности брокера. Общая схема должна быть описана в этой схеме:

![Общие сведения о проверке подлинности для пространственных привязок Azure](./media/spatial-anchors-aad-authentication.png)

В этом случае предполагается, что ваше приложение использует собственный механизм (например: Учетная запись Майкрософт, PlayFab, Facebook, Google ID, пользовательское имя пользователя и пароль и т. д.) для проверки подлинности в своей серверной службе. После проверки подлинности пользователей в серверной службе эта служба может получить маркер Azure AD, обмениваться маркером доступа для пространственных привязок Azure и вернуть ее обратно в клиентское приложение.

Маркер доступа Azure AD извлекается с помощью библиотеки ADAL, как описано в следующей документации: [https://docs.microsoft.com/azure/active-directory/develop/v1-overview](../../active-directory/develop/v1-overview.md); выполните действия, указанные в разделе "быстрое начало", который включает в себя следующее:

1.  Конфигурация в портал Azure:
    1.  Регистрация приложения в Azure AD:
        1.  В портал Azure перейдите к **Azure Active Directory**и выберите **Регистрация приложений** .
        2.  Выбор **регистрации нового приложения**
        3.  Введите имя приложения, в качестве типа приложения выберите **веб-приложение или API** и введите URL-адрес проверки подлинности для службы. Затем нажмите кнопку **создать**.
        4.  В этом приложении нажмите **Параметры**, а затем выберите вкладку **ключи** . Введите имя ключа, выберите длительность и нажмите кнопку **сохранить**. Не забудьте сохранить значение ключа, которое отображается в это время, так как его необходимо включить в код веб-службы.
    2.  Предоставьте приложению или пользователям доступ к Вашему ресурсу:
        1.  Перейдите к ресурсу пространственных привязок в портал Azure
        2.  Переключение на вкладку **управления доступом (IAM)**
        3.  Нажмите кнопку " **добавить назначение ролей** "
        1.  [Выберите роль](#role-based-access-control)
        2.  В поле **Выбор** введите имена созданных вами приложений и для которых необходимо назначить доступ. Если вы хотите, чтобы пользователи приложения имели разные роли для учетной записи пространственных привязок, необходимо зарегистрировать несколько приложений в Azure AD и назначить каждой отдельной роли. Затем реализуйте логику авторизации, чтобы использовать правильную роль для пользователей.  
    3.  Нажмите кнопку **сохранить**.
2.  В коде (Примечание. Вы можете использовать пример службы, включенный в GitHub):
    1.  Обязательно используйте идентификатор приложения, секрет приложения и URI перенаправления своего приложения Azure AD в качестве идентификатора клиента, секрета и параметров RedirectUri в ADAL.
    2.  Задайте для идентификатора клиента собственный идентификатор клиента Ааазуре в параметре Authority в ADAL.
    3.  В запросе маркера задайте ресурсу значение "https://sts.mixedreality.azure.com". 

При этом серверная служба может получить маркер Azure AD. Затем он может обмениваться данными с токеном MR, который будет возвращен клиенту. Использование маркера Azure AD для получения токена MR выполняется с помощью вызова RESTFUL. Вот пример вызова:

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

В котором заголовок авторизации имеет следующий формат:`Bearer <accoundId>:<accountKey>`

И ответ содержит токен MR в виде обычного текста.
 
Затем этот MR-токен возвращается клиенту. Клиентское приложение затем может задать его в качестве маркера доступа в конфигурации сеанса облака.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Управление доступом на основе ролей

Для управления уровнем доступа, предоставляемого приложениям, службам или пользователям Azure AD вашей службы, были созданы следующие роли для назначения в соответствии с учетными записями пространственных привязок Azure:

- **Учетная запись пространственного якоря владелец**. приложения или пользователи с этой ролью могут создавать пространственные привязки, запрашивать их и удалять. При проверке подлинности учетной записи с помощью ключей учетной записи роль **владельца учетной записи пространственных привязок** назначается участнику, прошедшему проверку подлинности. 
- **Участник учетной записи пространственных привязок**. приложения или пользователи с этой ролью могут создавать пространственные привязки, запрашивать их, но не могут удалять их. 
- **Средство чтения учетных записей пространственных привязок**. приложения или пользователи с этой ролью могут запрашивать только пространственные привязки, но не могут создавать новые, удалять существующие или обновлять метаданные на пространственных привязках. Обычно это используется для приложений, в которых некоторые пользователи изменяют среду, а другие могут отзывать только те привязки, которые были ранее помещены в эту среду.

## <a name="next-steps"></a>Следующие шаги

Создайте свое первое приложение с пространственными привязками Azure.

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
