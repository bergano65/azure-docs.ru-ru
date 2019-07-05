---
title: Проверка подлинности и авторизация для привязки пространственных Azure | Документация Майкрософт
description: Дополнительные сведения о различных способах, приложения или службы может пройти проверку подлинности для привязки пространственных Azure и уровни управления, что необходимо для доступа к пространственных привязки Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: c7ffa432c9311ba9d4ecf4ba82c375e2dad988d0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478548"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Проверка подлинности и авторизация для привязки пространственных Azure

В этом разделе будут рассмотрены различные способы, которые можно выполнить проверку подлинности для привязки пространственных Azure из приложения или веб-службы и способов, в котором можно использовать управление доступом на основе ролей в Azure Directory (Azure AD) для управления доступом к учетным записям пространственных привязки.  

## <a name="overview"></a>Обзор

![Обзор проверки подлинности для привязки пространственных Azure](./media/spatial-anchors-authentication-overview.png)

Чтобы получить доступ к данной учетной записи Azure пространственных привязки, клиентам необходимо сначала получить маркер доступа из Azure смешанной реальности маркеров безопасности службы (STS). Токены, полученные из службы маркеров безопасности live на 24 часа и содержат сведения для привязки пространственных служб принятия решений об авторизации в учетной записи и убедитесь, что только авторизованных участников можно получить доступ к этой учетной записи. 

Маркеры доступа можно получить в exchange из учетной записи либо нажмите сочетание клавиш или из Azure AD выданные маркеры. 

Ключи учетной записи позволяют быстро приступить к работе по использованию службы Azure пространственных привязки; Тем не менее перед развертыванием приложения в рабочей среде, рекомендуется обновить приложения для использования Azure проверку подлинности на основе AD. 

Токены проверки подлинности Azure AD можно получить двумя способами:

- Если вы создаете приложения предприятия, и ваша организация использует Azure AD как свою систему удостоверений, вы можете использовать Azure на основе пользователя проверки подлинности AD в приложении и предоставление доступа к учетным записям пространственных привязки, с помощью существующих групп безопасности Azure AD, или для пользователей в вашей организации. 
- В противном случае рекомендуется получать маркеры Azure AD в веб-службы, которые поддерживают приложение. Вспомогательные веб-службы используется рекомендуемый метод проверки подлинности для создания приложений, поскольку оно позволяет избежать внедрение учетные данные для доступа к Azure пространственных привязки в клиентском приложении. 

## <a name="account-keys"></a>Ключи учетной записи

С помощью ключей учетной записи для доступа к учетной записи Azure пространственных привязки — это самый простой способ приступить к работе. Ключи своей учетной записи можно найти на портале Azure. Перейдите к учетной записи и перейдите на вкладку «Ключи».

![Обзор проверки подлинности для привязки пространственных Azure](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Два ключа становятся доступными, оба одновременно допустимым для доступа к учетной записи пространственных привязки. Рекомендуется регулярно обновлять ключ, который позволяет получить доступ к учетной записи; имеющий два разделения enable допустимые ключи обновления бывают без простоя; необходимо также обновить первичный ключ и вторичный ключ. 

Пакет SDK имеет встроенную поддержку проверки подлинности с помощью ключей учетной записи; нужно просто задать свойство AccountKey cloudSession объекта. 

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

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrttabcppwinrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

***

После этого, пакет SDK будет обрабатывать обмен ключ учетной записи на маркер доступа и необходимости кэширования маркеров для приложения. 

> [!WARNING] 
> Для быстрого адаптацией, но во время разработки и создания прототипов только, рекомендуется использовать ключи учетной записи. Настоятельно рекомендуется не Отправка приложения в рабочей среде, с помощью ключа учетной записи внедренные в нем и вместо этого использовать Azure на пользователя или на основе службы проверки подлинности AD приближается к перечисленные далее.

## <a name="azure-ad-user-authentication"></a>Проверка подлинности пользователя Azure AD

Приложение предназначено для пользователей Azure Active Directory, рекомендуется использовать маркер Azure AD для пользователя, который можно получить с помощью библиотеки ADAL, как описано в следующей документации: [ https://docs.microsoft.com/azure/active-directory/develop/v1-overview ](../../active-directory/develop/v1-overview.md); вы следует выполнить действия, описанные в разделе «Краткие руководства», которые включают:

1. Настройка на портале Azure
    1.  Регистрация приложения в Azure AD в качестве **собственное приложение**. В процессе регистрации необходимо определить ли приложения должен быть мультитенантной, или нет и введите перенаправления, разрешенное в URL-адреса для вашего приложения.  
    2.  Предоставьте вашего приложения или пользователей с доступом к ресурсу: 
        1.  Перейдите к ресурсу пространственных привязки на портале Azure
        2.  Переключиться в режим **управление доступом (IAM)** вкладку
        3.  Попаданий **добавить назначение роли**
            1.  [Выберите роль](#role-based-access-control)
            2.  В **выберите** введите имя пользователя или пользователей, групп и/или приложения, к которому вы хотите назначить доступ. 
            3.  Попаданий **Сохранить**.
2. В коде:
    1.  Обязательно используйте **идентификатор приложения** и **Uri перенаправления** приложения Azure AD как **идентификатор клиента** и **RedirectUri** параметры в ADAL
    2.  Задайте сведения о клиенте:
        1.  Если приложение поддерживает **моей организации только**, замените это значение с вашей **Арендатора** или **имя_клиента** (например, contoso.microsoft.com)
        2.  Если приложение поддерживает **учетных записей в любой организации directory**, замените это значение с **организаций**
        3.  Если приложение поддерживает **пользователям учетных записей Майкрософт все**, замените это значение с **Common**
    3.  На ваш запрос маркера установите **ресурсов** для "https://sts.mixedreality.azure.com «. «resource» укажет в Azure AD, что ваше приложение запрашивает маркер для привязки пространственных Azure службы.  

Таким образом ваше приложение должно быть возможность получить из ADAL маркер Azure AD; можно задать этот маркер Azure AD как **authenticationToken** объекта конфигурации сеанса cloud. 

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

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

***

## <a name="azure-ad-service-authentication"></a>Служба проверки подлинности Azure AD

Для развертывания приложений, используя привязки пространственных Azure в рабочей среде рекомендуется использовать серверной службой, будет компонента service broker запросы на проверку подлинности. Должен быть общую схему, как описано в этой схеме:

![Обзор проверки подлинности для привязки пространственных Azure](./media/spatial-anchors-aad-authentication.png)

Здесь предполагается, что ваше приложение использует собственный механизм (например: Учетная запись Майкрософт, PlayFab, Facebook, Google идентификатор, пользовательского имени пользователя и пароля и т. д.) для проверки подлинности в серверной службой. После аутентификации своих пользователей во внутреннюю службу, который служба может получать маркер Azure AD его обмена на маркер доступа для привязки пространственных Azure и вернуть его обратно в клиентское приложение.

Маркер доступа Azure AD извлекается с использованием библиотеки ADAL, как описано в следующей документации: [ https://docs.microsoft.com/azure/active-directory/develop/v1-overview ](../../active-directory/develop/v1-overview.md); необходимо выполнить шаги, перечисленные в разделе «Краткие руководства», которые включают:

1.  Настройка на портале Azure:
    1.  Регистрация приложения в Azure AD:
        1.  На портале Azure перейдите к **Azure Active Directory**и выберите **регистрация приложений**
        2.  Выберите **Регистрация нового приложения**
        3.  Введите имя приложения, установите **веб-приложение или API** как тип приложения и введите URL-адрес проверки подлинности для службы. Нажмите клавишу **создать**.
        4.  В этом приложении попадания **параметры**, а затем выберите **ключи** вкладки. Введите имя ключа, выберите срок действия и попадания **Сохранить**. Обязательно сохраните значение ключа, который отображается в это время, так как он потребуется включить его в код веб службы.
    2.  Предоставьте приложение и/или пользователям доступ к ресурсу:
        1.  Перейдите к ресурсу пространственных привязки на портале Azure
        2.  Переключиться в режим **управление доступом (IAM)** вкладку
        3.  Попаданий **добавить назначение роли**
        1.  [Выберите роль](#role-based-access-control)
        2.  В **выберите** введите имя приложения, вы создали и для которого вы хотите назначить доступ. Пользователям приложения выполняют разные роли в учетной записи пространственных привязки следует следует зарегистрировать несколько приложений в Azure AD и назначать для каждой отдельной роли. Затем Реализуйте логику авторизации для использования правой роли для пользователей.  
    3.  Попаданий **Сохранить**.
2.  В коде (Примечание: можно использовать образец службы, включенные в GitHub):
    1.  Обязательно используйте идентификатор приложения, секрет приложения и перенаправления Uri приложения Azure AD как идентификатор клиента, секрет и параметров RedirectUri в ADAL
    2.  Задать идентификатор клиента для собственных AAAzure Добавьте идентификатор клиента в параметре центр в ADAL
    3.  На ваш запрос маркера установите **ресурсов** для "https://sts.mixedreality.azure.com " 

Таким образом внутренней службы можно получить маркер Azure AD. Он может затем обмениваться для маркера MR, он будет возвращать обратно клиенту. С помощью маркера Azure AD для извлечения токена MR выполняется путем вызова REST. Ниже приведен пример вызова.

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

Где заголовок авторизации имеет следующий формат: `Bearer <accoundId>:<accountKey>`

И ответ содержит токен MR в виде обычного текста.
 
Затем этот маркер MR возвращается клиенту. Клиентского приложения можно задать его как свой маркер доступа в облачную конфигурацию сеанса.

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

# <a name="c-ndktabcpp"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

***

## <a name="role-based-access-control"></a>Управление доступом на основе ролей

Чтобы упростить управление уровнем доступа для приложений, служб или Azure AD пользователей, назначение при необходимости с учетными записями Azure пространственных привязки будут созданы следующие роли:

- **Пространственные владелец учетной записи привязки**: приложения или пользователи с этой ролью, могут создавать Пространственные привязки, запросить их и удалять их. При проверке подлинности для учетной записи с помощью ключей учетной записи, **пространственных владелец учетной записи привязки** назначить роль субъекту-прошедшего проверку подлинности. 
- **Участник учетной записи пространственных привязки**: приложения или пользователи с этой ролью могут создавать Пространственные привязки, запроса, но их нельзя удалить. 
- **Пространственные привязки учетной записи чтения**: приложения или пользователи с этой ролью могут только запрашивать Пространственные привязки, но не может создавать новые, удалять существующие или обновление метаданных для привязки пространственных. Это обычно используется для приложений, где некоторые пользователи следите за среду, а другим пользователям только их все вспомнить привязки, помещенный в этой среде.

## <a name="next-steps"></a>Дальнейшие действия

Создание первого приложения с Azure пространственных привязки.

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
