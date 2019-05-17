---
title: Azure AD B2C (библиотека аутентификации Майкрософт для .NET) | Azure
description: Дополнительные сведения о специальные рекомендации при использовании Azure AD B2C с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c26a5007c2dcaa5d41be46f685f0f259866ca2c
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544061"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Использовать MSAL.NET выполнять вход пользователей с удостоверениями социальных сетей

Можно использовать для входа пользователей с удостоверениями социальных сетей, с помощью MSAL.NET [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). Azure AD B2C построена вокруг понятие политики. В MSAL.NET определение политики означает предоставление центр.

- При создании экземпляра общедоступного клиентского приложения, необходимо указать политику центра.
- Если вы хотите применить политику, вам нужно вызывать переопределение `AcquireTokenInteractive` содержащий `authority` параметра.

Эта страница предназначена для MSAL 3.x. Если вы заинтересованы в MSAL 2.x, см. в разделе [особенности Azure AD B2C в MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Центр для клиента Azure AD B2C и политики

— Центр сертификации, используемый `https://login.microsoftonline.com/tfp/{tenant}/{policyName}` где:

- `tenant` Имя клиента Azure AD B2C, 
- `policyName` Имя политики для применения (например «b2c_1_susi» для входа — в регистрации и входа).

В настоящее время рекомендации из Azure AD B2C, можно выполнить `b2clogin.com` центром сертификации. Например, `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"`. Дополнительные сведения см. в этом [документации](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Создание экземпляра приложения

При создании приложения, необходимо предоставить полномочия.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://fabrikamb2c.b2clogin.com/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Получение маркера для применения политики

Получение токена для Azure AD B2C защищенный API в приложении общедоступного клиента требует использования переопределений с центром:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

на:

- `policy` выполняется одна из предыдущих строк (например `PolicySignUpSignIn`).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` — Это метод, который находит учетную запись для данной политики. Например:

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
   foreach (var account in accounts)
   {
    string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
    if (userIdentifier.EndsWith(policy.ToLower()))
     return account;
   }
   return null;
  }
  ```

Применение политики (например, позволяя пользователю редактирования профиля и сброса пароля) в настоящее время выполняется путем вызова `AcquireTokenInteractive`. В случае эти две политики не используйте возвращенный маркер / результат проверки подлинности.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Особый случай EditProfile и ResetPassword политик

Если вы хотите обеспечить возможности которой конечные пользователи могут войти в удостоверения социальной сети, а затем измените свой профиль, вы хотите применить политики Azure AD B2C EditProfile. Способ сделать это, — путем вызова `AcquireTokenInteractive` с центр сертификации для этой политики и присвоить приглашение `Prompt.NoPrompt` во избежание диалоговое окно выбора учетной записи для отображения (как пользователь уже вошедшего в систему)

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
 IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
 try
 {
  var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                               .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                               .WithPrompt(Prompt.NoPrompt),
                               .WithB2CAuthority(App.AuthorityEditProfile)
                               .ExecuteAsync();
  DisplayBasicTokenInfo(authResult);
 }
 catch
 {
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Пароля владельца ресурса (ROPC) с Azure AD B2C
Дополнительные сведения о потоке ROPC, см. это [документации](v2-oauth-ropc.md).

Этот поток является **не рекомендуется** так, как приложение, запрашивание у пользователя пароль не является безопасным. Дополнительные сведения об этой проблеме см. в разделе [в этой статье](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

С помощью имени пользователя и пароля, вы предоставляя доступ к ряд действий:
- основные концепции современного удостоверения: пароль получает fished, воспроизведения. Поскольку у нас есть эта концепция секрет общей папки, которые могут быть перехвачены. Это несовместимо с без пароля.
- Пользователи, которым требуется проходить многофакторную Идентификацию, будет входить в систему (как никак не взаимодействует).
- Пользователи не будут иметь возможность единого входа.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Настройка последовательности ROPC в Azure AD B2C
В клиенте Azure AD B2C создать поток пользователя и выберите **вход с помощью ROPC**. Это позволит ROPC политики для вашего клиента. См. в разделе [настроить учетные данные пароля владельца ресурса потока](/azure/active-directory-b2c/configure-ropc) для получения дополнительных сведений.

`IPublicClientApplication` содержит метод:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Этот метод принимает в качестве параметров:
- *Областей* для запроса маркера доступа для.
- Объект *username*.
- Объект класса SecureString *пароль* для пользователя.

Не забывайте использовать центр, содержащий ROPC политики.

### <a name="limitations-of-the-ropc-flow"></a>Ограничения ROPC потока
 - Поток ROPC **работает только для локальных учетных записей** (где выполняется регистрация с помощью Azure AD B2C с помощью электронной почты или имя пользователя). Этот поток не работает, если федерация на любой из поставщиков удостоверений, поддерживаемых службой Azure AD B2C (Facebook, Google, и т.д.).
 - В настоящее время имеется **без маркера "id_token", возвращенный Azure AD B2C** при реализации потока ROPC из MSAL. Это означает, что объект учетной записи не может быть создан, таким образом в кэше, это будет учетная запись отсутствует и ни один пользователь. AcquireTokenSilent поток не будет работать в этом сценарии. Однако ROPC не отображает пользовательский Интерфейс, поэтому будет не влияет на взаимодействие с пользователем.

## <a name="google-auth-and-embedded-webview"></a>Проверка подлинности Google и Embedded Webview

Если вы являетесь разработчиком на Azure AD B2C с помощью Google как поставщик удостоверений мы recommand использовать обозреватель системы, как Google не допускает [проверки подлинности из встроенные веб-представления](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). В настоящее время `login.microsoftonline.com` является доверенным центром сертификации с помощью Google. Этот центр будет работать с embedded webview. Тем не менее с помощью `b2clogin.com` не является доверенным центром сертификации с помощью Google, поэтому пользователи не смогут проходить проверку подлинности.

Мы предусмотрим обновление для вики-сайте и это [проблема](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) Если внесенными изменениями.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Кэширование с помощью Azure AD B2C в MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Известная проблема с Azure AD B2C

Поддерживает MSAL.Net [кэш токена](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). Токен, кэширование ключ основан на утверждения, возвращаемые поставщиком удостоверений. В настоящее время MSAL.Net требуются два утверждения для создания ключа кэша маркера:  
- `tid` Это идентификатор клиента Azure AD, и 
- `preferred_username` 

Оба эти утверждения отсутствуют во многих сценариях Azure AD B2C. 

Влияние на заказчика — на то, что при попытке отобразить поле имени пользователя, вы получаете «Участвовали в ответе маркера» как значение? Если Да, это так, как Azure AD B2C не возвращают значение в IdToken для preferred_username из-за ограничений, связанных с учетных записей социальных сетей и внешних поставщиков удостоверений (IDP). Azure AD возвращает значение для preferred_username, поскольку известно, кто является пользователем, но для Azure AD B2C, так как пользователь может войти в систему с помощью локальной учетной записи, Facebook, Google, GitHub, и т.д. существует не постоянное значение для Azure AD B2C для использования для preferred_username. Чтобы разблокировать MSAL из развертывания кэша совместимости с помощью ADAL, мы решили использовать «Участвовали в ответе маркера» на нашей стороне, при работе с учетными записями Azure AD B2C при IdToken не возвращает ничего для preferred_username. MSAL должен возвращать значение для preferred_username для поддержания кэша совместимости с другими библиотеками.

### <a name="workarounds"></a>Обходные пути

#### <a name="mitigation-for-the-missing-tenant-id"></a>Устранение рисков для отсутствующего идентификатора клиента

Предлагаемый обходной путь — использовать [кэширование политикой](#acquire-a-token-to-apply-a-policy)

Кроме того, можно использовать `tid` утверждения, если вы используете [пользовательских политик B2C](https://aka.ms/ief), так как он предоставляет возможность возвращать дополнительные утверждения для приложения. Дополнительные сведения о [преобразование заявок](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Устранение рисков для «Участвовали в ответе маркера»
Один из вариантов — использовать утверждение «name» в качестве предпочтительное имя пользователя. Процесс упоминается в этом [B2C doc](/azure/active-directory-b2c/active-directory-b2c-reference-policies#frequently-asked-questions) -> «в столбце возвращаемое утверждения выберите утверждения, необходимо возвращать в маркерах авторизации, отправляемых приложению после успешного изменения профиля. Например выберите отображаемое имя, почтовый индекс».

## <a name="next-steps"></a>Дальнейшие действия 

Дополнительные сведения о получении маркеров в интерактивном режиме с помощью MSAL.NET для приложений Azure AD B2C, приведены в следующем примере.

| Образец | платформа | Описание|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, универсальной платформы Windows | Простое приложение Xamarin Forms, с отображением как с помощью MSAL.NET аутентификации пользователей с помощью Azure AD B2C и получить доступ к веб-API с помощью полученных маркеров.|
