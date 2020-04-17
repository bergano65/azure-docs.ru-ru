---
title: Azure AD B2C (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Узнайте о конкретных соображениях при использовании Azure AD B2C в библиотеке подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2019
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: d31cf3a4e024dc59b865d096cbd0829d50f61a1a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533961"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Используйте MSAL.NET для регистрации пользователей с социальными удостоверениями

Вы можете использовать MSAL.NET для регистрации пользователей с социальными удостоверениями, используя [Azure Active Directory B2C (Azure AD B2C).](https://aka.ms/aadb2c) Azure AD B2C построен вокруг понятия политики. В MSAL.NET, определение политики означает предоставление органа.

- При мгновенном обращении с публичным клиентским приложением необходимо указать политику во власти.
- Если вы хотите применить политику, необходимо вызвать `AcquireTokenInteractive` переопределение, содержащее `authority` параметр.

Эта страница для MSAL 3.x. Если вы заинтересованы в MSAL 2.x, пожалуйста, см [Azure AD B2C специфики в MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Орган для арендатора и политики Azure AD B2C

Полномочия по использованию, `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}` где:

- `azureADB2CHostname`— это имя арендатора Azure AD B2C `{your-tenant-name}.b2clogin.com`плюс узел (например),
- `tenant`— полное имя арендатора Azure AD B2C (например, `{your-tenant-name}.onmicrosoft.com`) или GUID для арендатора,
- `policyName`имя политики или поток пользователя для подачи заявки (например, "b2c_1_susi" для регистрации/входе).

Для получения дополнительной информации о органах Azure [documentation](/azure/active-directory-b2c/b2clogin)AD B2C, см.

## <a name="instantiating-the-application"></a>Мгновенное применение

При создании приложения необходимо предоставить полномочия.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Приобретение токена для применения политики

Приобретение токена для защищенного API Azure AD B2C в общеклиентном приложении требует от вас использования переопределения с авторитетом:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

вставьте

- `policy`является одной из предыдущих строк `PolicySignUpSignIn`(например).
- `ParentActivityOrWindow`требуется для Android (активность) и для других платформ, поддерживающих родительский uI, таких как окна в Windows и UIViewController в iOS. Более подробную информацию можно увидеть [в диалоге uI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`— это метод, который находит учетную запись для данной политики. Пример:

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

Применение политики или потока пользователей (например, разрешение конечному пользователю отсечь `AcquireTokenInteractive`свой профиль или сбросить пароль) в настоящее время осуществляется путем вызова. В случае этих двух политик вы не используете результат возврата маркера/аутентификации.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Особый случай политикEdProfile и ResetPassword

Если требуется предоставить опыт, когда конечные пользователи вписываются в социальную идентификацию, а затем отсваивают свой профиль, необходимо применить политику Azure AD Edit Profile. Это можно сделать, `AcquireTokenInteractive` позвонив в конкретные органы `Prompt.NoPrompt` для этой политики, и оперативно, чтобы предотвратить отображение диалога выбора учетной записи (поскольку пользователь уже вписался в систему и имеет активную сессию cookie).

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
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Учетные данные паролей владельца ресурсов (ROPC) с Azure AD B2C
Для получения более подробной информации о потоке ROPC, пожалуйста, смотрите эту [документацию](v2-oauth-ropc.md).

Этот поток **не рекомендуется,** потому что ваше приложение просит у пользователя пароль не является безопасным. Для получения дополнительной информации об этой проблеме, [см.](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)

Используя имя пользователя /пароль, вы отказываетесь от ряда вещей:
- Основные принципы современной идентичности: пароль получает ловил, воспроизводил. Потому что у нас есть эта концепция секрета акций, которые могут быть перехвачены. Это несовместимо с безпарочкой.
- Пользователи, которым необходимо сделать МИД, не смогут войти в систему (так как нет взаимодействия).
- Пользователи не смогут делать одиночный входной знак.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Настройка потока ROPC в Azure AD B2C
В вашем наемщике Azure AD B2C создайте новый поток пользователей и выберите **регистрацию в использовании ROPC.** Это позволит политику ROPC для вашего арендатора. Для получения более подробной информации смотрите [поток учетных данных владельца паролей владельца ресурса.](/azure/active-directory-b2c/configure-ropc)

`IPublicClientApplication`содержит метод:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Этот метод принимает в качестве параметров:
- *Области* для запроса токена доступа.
- *Имя пользователя*.
- *Пароль* SecureString для пользователя.

Не забудьте использовать полномочия, содержащие политику ROPC.

### <a name="limitations-of-the-ropc-flow"></a>Ограничения потока ROPC
 - Поток ROPC **работает только для локальных учетных записей** (где вы регистрируетесь в Azure AD B2C с помощью электронной почты или имени пользователя). Этот поток не работает, если федерендля ни один из поставщиков идентификационных данных, поддерживаемых Azure AD B2C (Facebook, Google и т.д.).

## <a name="google-auth-and-embedded-webview"></a>Google Auth и встроенный веб-вью

Если вы являетесь разработчиком Azure AD B2C, использующим Google в качестве поставщика идентификационных данных, мы вновь используем системный браузер, так как Google не разрешает [аутентификацию от встроенных веб-просмотров.](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html) В `login.microsoftonline.com` настоящее время, является доверенным органом с Google. Используя этот орган будет работать со встроенным веб-вью. Однако `b2clogin.com` использование не является доверенным органом Google, поэтому пользователи не смогут проверить подлинность.

Мы предоставим обновленную информацию по этой [проблеме,](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) если что-то изменится.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Кэширование с Azure AD B2C в MSAL.Net

### <a name="known-issue-with-azure-ad-b2c"></a>Известная проблема с Azure AD B2C

MSAL.Net поддерживает [кэш маркеров.](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet) Ключ кэширования маркеров основан на претензиях, возвращенных поставщиком идентификационных данных. В настоящее время MSAL.Net требуется две претензии для создания ключа кэша токенов:
- `tid`идентификатор арендатора Azure AD, и
- `preferred_username`

Обе эти утверждения отсутствуют во многих сценариях Azure AD B2C.

Влияние на клиента заключается в том, что при попытке отобразить поле имени пользователя вы получаете значение "Missing from the token response"? Если это так, то это связано с тем, что Azure AD B2C не возвращает значение в IdToken для preferred_username из-за ограничений с социальными учетными записями и внешними поставщиками идентификационных данных (IdPs). Azure AD возвращает значение для preferred_username, потому что он знает, кто пользователь, но для Azure AD B2C, потому что пользователь может войти в локальную учетную запись, Facebook, Google, GitHub и т.д. Нет последовательного значения для Azure AD B2C, чтобы использовать для preferred_username. Чтобы отблокировать MSAL от внедрения совместимости кэша с ADAL, мы решили использовать "Missing from the token response" на нашем конце при работе с учетными записями Azure AD B2C, когда IdToken ничего не возвращает для preferred_username. MSAL должен вернуть значение для preferred_username для поддержания совместимости кэша между библиотеками.

### <a name="workarounds"></a>Обходные решения

#### <a name="mitigation-for-the-missing-tenant-id"></a>Смягчение для отсутствующих идентификаторов арендатора

Предлагаемый обходной путь заключается в использовании [кэшинга по политике](#acquire-a-token-to-apply-a-policy)

Кроме того, вы `tid` можете использовать претензию, если вы используете [пользовательские политики B2C,](https://aka.ms/ief)поскольку она предоставляет возможность возврата дополнительных претензий к приложению. Чтобы узнать больше о [трансформации претензий](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Смягчение меры для "Пропавших без вести из ответа токенов"
Один из вариантов заключается в использовании "имя" претензии в качестве предпочтительного имени пользователя. Этот процесс упоминается в этом [B2C doc->](../../active-directory-b2c/user-flow-overview.md) "В столбце претензии return выберите требования, которые вы хотите вернуть в токенах авторизации, отправленных обратно в приложение после успешного опыта редактирования профиля. Например, выберите имя дисплея, почтовый индекс."

## <a name="next-steps"></a>Следующие шаги

Более подробная информация о приобретении токенов в интерактивном режиме с MSAL.NET для приложений Azure AD B2C приведена в следующем примере.

| Пример | Платформа | Описание|
|------ | -------- | -----------|
|[активный каталог-b2c-xamarin-родной](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Простое приложение Xamarin Forms, демонстрирующее, как использовать MSAL.NET для проверки подлинности пользователей через Azure AD B2C и получить доступ к веб-API с полученными токенами.|
