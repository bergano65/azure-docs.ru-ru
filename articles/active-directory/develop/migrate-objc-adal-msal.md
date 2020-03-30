---
title: ADAL к msAL руководство по миграции (MSAL iOS/macOS) Azure
titleSuffix: Microsoft identity platform
description: Узнайте различия между MSAL для iOS/macOS и Библиотекой аутентификации Azure AD для ObjectiveC (ADAL. ObjC) и как перейти на MSAL для iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 6050bdc8c2600998b9804b04b62102e74612719f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085178"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Перенести приложения в MSAL для iOS и macOS

Библиотека подлинности активных каталогов Azure[(ADAL Objective-C)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)была создана для работы с учетными записями Active Directory Azure через конечную точку v1.0.

Библиотека аутентификации Майкрософт для iOS и macOS (MSAL) создана для работы со всеми идентификаторами Майкрософт, такими как учетные записи Active Directory (Azure AD), личные учетные записи Майкрософт и учетные записи Azure AD B2C через платформу идентификации Майкрософт (формально Azure AD v2.0 конечная точка).

Платформа идентификации Майкрософт имеет несколько ключевых отличий от Azure Active Directory v1.0. В этой статье освещаются эти различия и содержатся рекомендации по переносу приложения из ADAL в MSAL.

## <a name="adal-and-msal-app-capability-differences"></a>Различия в возможностях приложений ADAL и MSAL

### <a name="who-can-sign-in"></a>Каким учетным записям разрешен вход

* ADAL поддерживает только рабочие и школьные учетные записи, также известные как учетные записи Azure AD.
* MSAL поддерживает личные учетные записи Майкрософт (MSA), такие как Hotmail.com, Outlook.com и Live.com.
* MSAL поддерживает рабочие и школьные учетные записи, а также учетные записи Azure AD B2C.

### <a name="standards-compliance"></a>Соответствие стандартам

* Конечная точка платформы microsoft- идентификационных данных соответствует стандартам OAuth 2.0 и OpenId Connect.

### <a name="incremental-and-dynamic-consent"></a>Добавочное и динамическое согласие

* Конечная точка Azure Active Directory v1.0 требует, чтобы все разрешения были объявлены заранее во время регистрации заявки. Это означает, что эти разрешения статичны.
* Платформа идентификации Майкрософт позволяет динамически запрашивать разрешения. Приложения могут запрашивать разрешения только по мере необходимости и запрашивать больше, так как они нужны приложению.

Подробнее о различиях между Azure Active Directory v1.0 и платформой идентификации Майкрософт читайте в чем вопрос [ещё обновление платформы Microsoft (v2.0)?.](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

## <a name="adal-and-msal-library-differences"></a>Различия в библиотечных различиях ADAL и MSAL

API MSAL отражает несколько ключевых отличий между Azure AD v1.0 и платформой идентификации Майкрософт.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication вместо ADAuthenticationКонтекст

`ADAuthenticationContext`является первым объектом, который создает приложение ADAL. Он представляет собой мгновенное aDAL. Приложения создают новый `ADAuthenticationContext` экземпляр для каждого облака Active Directory Azure и комбинации арендаторов (авторитета). То `ADAuthenticationContext` же самое можно использовать для получения токенов для нескольких публичных клиентских приложений.

В MSAL основное взаимодействие `MSALPublicClientApplication` происходит через объект, который моделируется после [OAuth 2.0 Public Client.](https://tools.ietf.org/html/rfc6749#section-2.1) Один экземпляр `MSALPublicClientApplication` может использоваться для взаимодействия с несколькими облаками AAD и арендаторами без необходимости создания нового экземпляра для каждого органа. Для большинства приложений достаточно одного `MSALPublicClientApplication` экземпляра.

### <a name="scopes-instead-of-resources"></a>Области вместо ресурсов

В ADAL приложение должно было предоставить `https://graph.microsoft.com` идентификатор *ресурсов,* как приобрести токены из azure Active Directory v1.0 endpoint. Ресурс может определить ряд областей, или oAuth2Permissions в манифесте приложения, который он понимает. Это позволило приложениям-клиентам запрашивать токены из этого ресурса для определенного набора областей, предварительно определенных во время регистрации приложения.

В MSAL вместо одного идентификатора ресурсов приложения предоставляют набор областей для запроса. Область — это идентификатор ресурса, за которым следует имя разрешения в ресурсе-разрешении формы. Например `https://graph.microsoft.com/user.read`.

Существует два способа предоставления областей в MSAL:

* Предоставьте список всех разрешений, необходимых вашим приложениям. Пример: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    В этом случае приложение `directory.read` запрашивает разрешения и `directory.write` разрешения. Пользователю будет предложено дать согласие на эти разрешения, если он не дал им согласия ранее для этого приложения. Приложение также может получить дополнительные разрешения, на которые пользователь уже дал согласие на приложение. Пользователю будет предложено только дать согласие на новые разрешения или разрешения, которые не были предоставлены.

* Область `/.default`.

Это встроенный объем для каждого приложения. Это относится к статическому списку разрешений, настроенных при регистрации приложения. Его поведение похоже на `resource`поведение . Это может быть полезно при миграции, чтобы гарантировать, что аналогичный набор областей и пользовательский опыт поддерживается.

Чтобы использовать `/.default` область применения, прибвывай `/.default` к идентификатору ресурса. Например: `https://graph.microsoft.com/.default`. Если ваш ресурс заканчивается`/`счертоть `/.default`(), вы все равно должны придатиться, в`//`том числе ведущих вперед слэш, в результате чего область, которая имеет двойной вперед черту ( ) в нем.

Вы можете прочитать более подробную информацию об использовании области "/.по умолчанию" [здесь](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope)

### <a name="supporting-different-webview-types--browsers"></a>Поддержка различных типов WebView & браузеров

ADAL поддерживает UIWebView/WKWebView только для iOS, а WebView для macOS. MSAL для iOS поддерживает больше возможностей для отображения веб-контента при запросе кода авторизации и больше не поддерживает; `UIWebView` которые могут улучшить пользовательский опыт и безопасность.

По умолчанию MSAL на iOS использует [ASWebAuthenticationAtionSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc), который является веб-компонентом, который Apple рекомендует для проверки подлинности на устройствах iOS 12. Он обеспечивает преимущества Единого регистрации (SSO) через совместное использование файлов cookie между приложениями и браузером Safari.

Вы можете использовать другой веб-компонент в зависимости от требований приложения и пользовательского интерфейса. Посмотреть [поддерживаемые типы веб-представлений](customize-webviews.md) для получения дополнительных опций.

При переходе из ADAL `WKWebView` в MSAL обеспечивает пользовательский опыт, наиболее похожий на ADAL на iOS и macOS. Мы рекомендуем вам `ASWebAuthenticationSession` мигрировать на iOS, если это возможно. Для macOS мы рекомендуем `WKWebView`вам использовать .

### <a name="account-management-api-differences"></a>Различия в API управления счетами

При вызове `acquireToken()` методов ADAL или `acquireTokenSilent()` `ADUserInformation` вы получаете объект, `id_token` содержащий список претензий из учетной записи, представляющей аутентификатную учетную запись. Кроме того, `ADUserInformation` `userId` возвращает на `upn` основе претензии. После первоначального приобретения интерактивных токенов `userId` ADAL ожидает, что разработчик будет предоставлять все молчаливые звонки.

ADAL не предоставляет API для получения известных идентификаторов пользователей. Он полагается на приложение, чтобы сохранить и управлять этими учетными записями.

MSAL предоставляет набор AIS для списка всех учетных записей, известных MSAL без необходимости приобретать токен.

Как и ADAL, MSAL возвращает информацию об `id_token`учетной записи, в которую содержится список претензий от . Это часть `MSALAccount` объекта внутри `MSALResult` объекта.

MSAL предоставляет набор AA для удаления учетных записей, что делает удаленные учетные записи недоступными для приложения. После удаления учетной записи последующие вызовы приобретения токенов побуждают пользователя сделать интерактивное приобретение токенов. Удаление учетной записи относится только к клиенту приложения, которое запустило его, и не удаляет учетную запись из других приложений, работающих на устройстве или из системного браузера. Это гарантирует, что пользователь продолжает иметь sSO опыт на устройстве даже после подписания из отдельного приложения.

Кроме того, MSAL также возвращает идентификатор учетной записи, который может быть использован для запроса маркера позже. Тем не менее, идентификатор учетной записи (доступный через `identifier` свойство объекта) `MSALAccount` не отображается, и вы не можете предположить, в каком формате он находится, и вы не должны пытаться интерпретировать или разбирать его.

### <a name="migrating-the-account-cache"></a>Миграция кэша учетной записи

При миграции из ADAL приложения обычно хранят `userId`ADAL, который `identifier` не требуется MSAL. В качестве одноразового шага миграции приложение может загонять учетную запись MSAL с помощью userId ADAL со следующим API:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Этот API считывает кэш MSAL и ADAL, чтобы найти учетную запись aDAL userId (UPN).

Если учетная запись найдена, разработчик должен использовать учетную запись для приобретения бесшумных токенов. Первое приобретение бесшумного токена позволит эффективно обновить учетную запись, и разработчик получит`identifier`идентификатор учетной записи msAL в результате MSAL ( ). После этого `identifier` следует использовать только для поиска учетной записи с помощью следующего API:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Хотя можно продолжать использовать ADAL `userId` для всех операций в `userId` MSAL, так как основан на UPN, это зависит от нескольких ограничений, которые приводят к плохой пользовательский опыт. Например, если UPN изменяется, пользователь должен войти снова. Мы рекомендуем всем приложениям использовать `identifier` неотображаемую учетную запись для всех операций.

Подробнее о [миграции состояния кэша](sso-between-adal-msal-apps-macos-ios.md).

### <a name="token-acquisition-changes"></a>Изменения приобретения токенов

MSAL вводит некоторые изменения вызова приобретения токенов:

* Как и `acquireTokenSilent` ADAL, всегда приводит к молчаливому запросу.
* В отличие `acquireToken` от ADAL, всегда приводит к пользовательской действия пользовательского интерфейса либо через веб-представление или приложение Microsoft Authenticator. В зависимости от состояния SSO внутри веб-взрения/Microsoft Authenticator, пользователю может быть предложено ввести свои учетные данные.
* В `acquireToken` ADAL, `AD_PROMPT_AUTO` с первых попыток молчаливого приобретения токенов, и только показывает uI, если молчаливый запрос не удается. В MSAL, эта логика может `acquireTokenSilent` быть `acquireToken` достигнута путем первого вызова и только вызова, если молчание приобретения не удается. Это позволяет разработчикам настроить пользовательский опыт перед началом интерактивного приобретения токенов.

### <a name="error-handling-differences"></a>Различия в обработке ошибок

MSAL обеспечивает большую ясность между ошибками, которые могут быть обработаны вашим приложением, и ошибками, требующими вмешательства пользователя. Есть ограниченное количество ошибок, которые должен обрабатывать разработчик:

* `MSALErrorInteractionRequired`: Пользователь должен сделать интерактивный запрос. Это может быть вызвано различными причинами, такими как просроченная сессия проверки подлинности, изменение политики условного доступа, срок действия маркера обновления или аннулирования, отсутствие действительных токенов в кэше и так далее.
* `MSALErrorServerDeclinedScopes`: Запрос не был полностью завершен, и некоторые области не были предоставлены доступ. Это может быть вызвано отказом пользователя от согласия на одну или несколько областей.

Обработка всех других ошибок в [ `MSALError` списке](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) не является обязательной. Вы можете использовать информацию в этих ошибках для улучшения пользовательского опыта.

Прослежись [по исключениям и ошибкам обработки с помощью MSAL](msal-handling-exceptions.md) для получения дополнительной информации об обработке ошибок MSAL.

### <a name="broker-support"></a>Поддержка брокера

MSAL, начиная с версии 0.3.0, обеспечивает поддержку посреднической аутентификации с помощью приложения Microsoft Authenticator. Microsoft Authenticator также обеспечивает поддержку сценариев условного доступа. Примеры сценариев условного доступа включают политики соответствия устройств, которые требуют, чтобы пользователь зарегистрировал устройство через Intune или зарегистрировался в AAD, чтобы получить токен. И политики управления мобильными приложениями (MAM) Условный доступ, которые требуют доказательства соответствия, прежде чем ваше приложение может получить токен.

Для того, чтобы брокер для вашего приложения:

1. Зарегистрируйте брокера совместимый перенаправить URI формат для приложения. Брокер совместимый перенаправить URI `msauth.<app.bundle.id>://auth`формат . Замените `<app.bundle.id>` идентификатор пакета приложения. Если вы мигрируете из ADAL и ваше приложение уже было брокером способным, нет ничего лишнего вам нужно сделать. Ваш предыдущий перенаправить URI полностью совместим с MSAL, так что вы можете перейти к шагу 3.

2. Добавьте схему redirect URI в файл info.plist вашего приложения. Для по умолчанию MSAL перенаправить `msauth.<app.bundle.id>`URI, формат . Пример:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Добавьте следующие схемы в Info.plist вашего приложения в рамках LSApplication-ЗапросыСхемы:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Добавьте следующее в файл AppDelegate.m для обработки обратных вызовов: Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>B2B (бизнес-бизнес)

В ADAL создаются отдельные `ADAuthenticationContext` экземпляры для каждого клиента, для которых приложение запрашивает токены. Это больше не является требованием в MSAL. В MSAL можно создать единый `MSALPublicClientApplication` экземпляр и использовать его для любого облака AAD и организации, указав другой орган для приобретенияToken и приобретения вызововTokenSilent.

## <a name="sso-in-partnership-with-other-sdks"></a>SSO в партнерстве с другими SDK

MSAL для iOS может достичь SSO с помощью единого кэша со следующими SDK:

- ADAL Объектив-C 2.7.x
- MSAL.NET для Xamarin 2.4.x
- ADAL.NET для Xamarin 4.4.x

SSO достигается с помощью обмена брелоком iOS и доступен только между приложениями, опубликованными из той же учетной записи разработчика Apple.

SSO через iOS обмена брелока является единственным молчаливым типом SSO.

На macOS, MSAL может достичь SSO с другими MSAL для iOS и macOS на основе приложений и ADAL объективной-C-приложений.

MSAL на iOS также поддерживает два других типа SSO:

* SSO через веб-браузер. MSAL для iOS поддерживает `ASWebAuthenticationSession`, который обеспечивает SSO через файлы cookie, совместно используемые между другими приложениями на устройстве и, в частности, браузером Safari.
* SSO через брокера аутентификации. На устройстве iOS Microsoft Authenticator выступает в качестве брокера аутентификации. Он может следовать политикам условного доступа, например, требующему совместимого устройства, и предоставляет SSO для зарегистрированных устройств. MSAL SDKs, начиная с версии 0.3.0, поддерживают брокера по умолчанию.

## <a name="intune-mam-sdk"></a>Intune MAM SDK

[Intune MAM SDK](https://docs.microsoft.com/intune/app-sdk-get-started) поддерживает MSAL для iOS, начиная с [версии 11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)

## <a name="msal-and-adal-in-the-same-app"></a>MSAL и ADAL в одном приложении

Версия ADAL 2.7.0, и выше, не может сосуществовать с MSAL в том же приложении. Основная причина связана с общим общим кодом общего подмодуля. Поскольку Objective-C не поддерживает области имен, если вы добавите в приложение как aDAL, так и MSAL, будет два экземпляра одного класса. Там нет гарантии, для которых один получает выбрали во время выполнения. Если оба SDK используют одну и ту же версию конфликтующего класса, ваше приложение все равно может работать. Однако, если это другая версия, ваше приложение может испытывать неожиданные сбои, которые трудно диагностировать.

Запуск ADAL и MSAL в одном производственном приложении не поддерживается. Однако, если вы просто тестируете и мигрируете пользователям из ADAL Objective-C в MSAL для iOS и macOS, вы можете продолжать использовать [ADAL Objective-C 2.6.10.](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10) Это единственная версия, которая работает с MSAL в том же приложении. Для этой версии ADAL не будет новых обновлений функций, поэтому она должна использоваться только для целей миграции и тестирования. Ваше приложение не должно полагаться на сосуществование ADAL и MSAL в долгосрочной перспективе.

Сосуществование ADAL и MSAL в одном приложении не поддерживается.
Сосуществование ADAL и MSAL между несколькими приложениями полностью поддерживается.

## <a name="practical-migration-steps"></a>Практические шаги по миграции

### <a name="app-registration-migration"></a>Миграция регистрации приложений

Вам не нужно менять существующее приложение AAD, чтобы переключиться на MSAL и включить учетные записи AAD. Однако, если приложение на базе ADAL не поддерживает посреднической аутентификации, вам необходимо зарегистрировать новый перенаправить URI для приложения, прежде чем вы сможете перейти на MSAL.

Перенаправление URI должно быть в `msauth.<app.bundle.id>://auth`этом формате: . Замените `<app.bundle.id>` идентификатор пакета приложения. Укажите перенаправление URI на [портале Azure](https://aka.ms/MobileAppReg).

Только для iOS для поддержки проверки подлинности на основе сертификата необходимо зарегистрировать дополнительное перенаправление URI `msauth://code/<broker-redirect-uri-in-url-encoded-form>`в вашем приложении и портале Azure в следующем формате: . Например `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`.

Мы рекомендуем всем приложениям зарегистрировать как перенаправить URI.

Если вы хотите добавить поддержку для поэтапного согласия, выберите API и разрешения, к которых настроено ваше приложение, чтобы запросить доступ к нему в регистрации приложения под вкладкой **aPI-разрешений.**

Если вы мигрируете из ADAL и хотите поддерживать учетные записи AAD и MSA, существующая регистрация приложений должна быть обновлена для поддержки обоих. Мы не рекомендуем обновлять существующее производственное приложение для поддержки AAD и MSA сразу. Вместо этого создайте другой идентификатор клиента, который поддерживает AAD и MSA для тестирования, и после того, как вы проверите, что все сценарии работают, обновите существующее приложение.

### <a name="add-msal-to-your-app"></a>Добавление MSAL в приложение

Вы можете добавить MSAL SDK в приложение с помощью предпочтительного инструмента управления пакетами. Смотрите [подробные инструкции здесь](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation).

### <a name="update-your-apps-infoplist-file"></a>Обновление файла Info.plist вашего приложения

Только для iOS добавьте схему uri перенаправления приложения в файл info.plist. Для ADAL брокерсовместимых приложений, он должен быть там уже. Схема перенаправления MSAL MSAL URI будет `msauth.<app.bundle.id>`в формате: .  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Добавьте следующие схемы в Info.plist вашего приложения под `LSApplicationQueriesSchemes`.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Обновление кода AppDelegate

Только для iOS добавьте следующее в файл AppDelegate.m:

Objective-C.

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**Если вы используете Xcode 11,** вы должны `SceneDelegate` поместить MSAL обратный вызов в файл вместо.
Если UISceneDelegate и UIApplicationDelegate поддерживаются для совместимости с более старыми версиями iOS, то обратный вызов MSAL потребуется поместить в оба файла.

Objective-C.

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

Это позволяет MSAL обрабатывать ответы от брокера и веб-компонента.
Это не было необходимо в ADAL, так как он "закачивал" методы делегирования приложений автоматически. Добавление его вручную менее подвержено ошибкам и дает приложению больше контроля.

### <a name="enable-token-caching"></a>Включение кэширования маркеров

По умолчанию MSAL кэширует маркеры приложения в цепочку ключей iOS или macOS. 

Чтобы включить кэширования маркеров:
1. Убедитесь, что приложение подписано правильно
2. Перейдите к настройкам проекта Xcode > **вкладка** > Возможности**Включения обмена ключами**
3. Нажмите **+** и введите следующий вход **Группы Ключей:** 3.a Для iOS, введите `com.microsoft.adalcache` 3.b Для ввода macOS`com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>Создайте MSALPublicClientApplication и переключитесь на его приобретениеToken и приобретите вызовы TokeSilent

Вы можете `MSALPublicClientApplication` создать с помощью следующего кода:

Objective-C.

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Затем позвоните в API управления учетными записями, чтобы узнать, есть ли учетные записи в кэше:

Objective-C.

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift:

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



или прочитать все счета:

Objective-C.

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift:

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Если учетная запись найдена, `acquireTokenSilent` позвоните в MSAL API:

Objective-C.

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в [Потоки проверки подлинности и сценарии приложений](authentication-flows-app-scenarios.md)
