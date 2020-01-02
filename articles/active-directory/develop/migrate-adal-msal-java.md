---
title: Инструкции по миграции ADAL в MSAL для Java | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как перенести приложение Java (Библиотека проверки подлинности Azure Active Directory) в библиотеку проверки подлинности Майкрософт (MSAL).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e99cee3f21a4e0088fa97dcbec8fdcfdf982d80
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917392"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Инструкции по миграции ADAL в MSAL для Java

В этой статье описываются изменения, которые необходимо внести, чтобы перенести приложение, Azure Active Directory использующее библиотеку проверки подлинности (MSAL), с помощью библиотеки аутентификации Майкрософт (ADAL).

Как библиотека проверки подлинности Майкрософт для Java (MSAL4J), так и библиотека аутентификация Azure AD для Java (ADAL4J) используются для проверки подлинности сущностей Azure AD и маркеров запросов из Azure AD. До настоящего момента большинство разработчиков работали с Azure AD для разработчиков (версия 1.0) для проверки подлинности удостоверений Azure AD (рабочих и учебных учетных записей), запрашивая маркеры с помощью библиотеки аутентификация Azure AD Library (ADAL).

MSAL предоставляет следующие преимущества:

- Так как в нем используется новая конечная точка платформы идентификации Майкрософт, вы можете выполнить проверку подлинности более широкого набора удостоверений Майкрософт, таких как удостоверения Azure AD, учетные записи Майкрософт, социальные и локальные учетные записи, через Azure AD Business для потребителей (B2C).
- Пользователи получат лучшие возможности единого входа.
- Приложение может включить добавочное согласие, и поддержка условного доступа упрощается.

MSAL для Java — это библиотека проверки подлинности, которую мы рекомендуем использовать с платформой Microsoft Identity. Новые функции не будут реализованы в ADAL4J. Все усилия, которые переносятся вперед, сосредоточены на улучшении MSAL.

## <a name="differences"></a>Различия

Если вы работали с конечной точкой Azure AD для разработчиков (v 1.0) (и ADAL4J), вам может потребоваться ознакомиться с [другими сведениями о конечной точке платформы Microsoft Identity Platform (v 2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison).

## <a name="scopes-not-resources"></a>Области вместо ресурсов

ADAL4J получает маркеры для ресурсов, тогда как MSAL для Java получает маркеры для областей. Для нескольких MSAL для классов Java требуется параметр областей. Этот параметр представляет собой список строк, объявляющих требуемые разрешения и ресурсы. См. раздел [области Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference) для просмотра примеров областей.

## <a name="core-classes"></a>Основные классы

В ADAL4J класс `AuthenticationContext` представляет подключение к службе маркеров безопасности (STS) или серверу авторизации через центр сертификации. Однако MSAL для Java разрабатывается на основе клиентских приложений. Он предоставляет два отдельных класса: `PublicClientApplication` и `ConfidentialClientApplication` для представления клиентских приложений.  Последнее, `ConfidentialClientApplication`, представляет приложение, предназначенное для безопасного сохранения секрета, такого как идентификатор приложения для управляющего приложения.

В следующей таблице показано, как функции ADAL4J сопоставляются с новым MSAL для функций Java:

| Метод ADAL4J| Метод MSAL4J|
|------|-------|
|acquireToken (строковый ресурс, учетные данные ClientCredential, обратный вызов AuthenticationCallback) | acquireToken (Клиенткредентиалпараметерс)|
|acquireToken (строковый ресурс, утверждение Клиентассертион, обратный вызов AuthenticationCallback)|acquireToken (Клиенткредентиалпараметерс)|
|acquireToken (строковый ресурс, учетные данные Асимметриккэйкредентиал, обратный вызов AuthenticationCallback)|acquireToken (Клиенткредентиалпараметерс)|
|acquireToken (строковый ресурс, строка clientId, строковое имя пользователя, строковый пароль, обратный вызов AuthenticationCallback)| acquireToken (Усернамепассвордпараметерс)|
|acquireToken (строковый ресурс, строка clientId, строковое имя пользователя, строка Password = null, обратный вызов AuthenticationCallback)|acquireToken (Интегратедвиндовсаусентикатионпараметерс)|
|acquireToken (строковый ресурс, Усерассертион Усерассертион, учетные данные ClientCredential, обратный вызов AuthenticationCallback)| acquireToken (Онбехалфофпараметерс)|
|acquireTokenByAuthorizationCode () | acquireToken (Аусоризатионкодепараметерс) |
| Аккуиредевицекоде () и Аккуиретокенбидевицекоде ()| acquireToken (Девицекодепараметерс)|
|Аккуиретокенбирефрештокен ()| Аккуиретокенсилентли (Силентпараметерс)|

## <a name="iaccount-instead-of-iuser"></a>Иаккаунт вместо IUser

ADAL4J пользователей. Хотя пользователь представляет одного человека или агента программного обеспечения, он может иметь одну или несколько учетных записей в системе идентификации Майкрософт. Например, пользователь может иметь несколько учетных записей Azure AD, Azure AD B2C или Microsoft Personal.

MSAL для Java определяет концепцию учетной записи через интерфейс `IAccount`. Это критическое изменение от ADAL4J, но это хороший фактор, так как он захватывает тот факт, что один пользователь может иметь несколько учетных записей и, возможно, даже в разных каталогах Azure AD. MSAL for Java предоставляет лучшую информацию в гостевых сценариях, так как предоставляются сведения о домашних учетных записях.

## <a name="cache-persistence"></a>Сохраняемость кэша

ADAL4J не поддерживает кэш маркеров.
MSAL для Java добавляет [кэш маркеров](msal-acquire-cache-tokens.md) для упрощения управления жизненным циклом маркера путем автоматического обновления токенов с истекшим сроком действия, когда это возможно, и предотвращения ненужных запросов пользователя на предоставление учетных данных по возможности.

## <a name="common-authority"></a>Общий центр

В версии 1.0 при использовании центра `https://login.microsoftonline.com/common` пользователи могут входить в систему с помощью любой учетной записи Azure Active Directory (AAD) (для любой организации).

При использовании центра `https://login.microsoftonline.com/common` в версии 2.0 пользователи могут входить в систему с любой организацией AAD или даже с помощью личной учетной записи Майкрософт (MSA). Если вы хотите ограничить учетную запись AAD для входа в MSAL для Java, необходимо использовать центр `https://login.microsoftonline.com/organizations` (то же поведение, что и в случае с ADAL4J). Чтобы указать центр сертификации, задайте параметр `authority` в методе [публикклиентаппликатион. Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) при создании класса `PublicClientApplication`.

## <a name="v10-and-v20-tokens"></a>Маркеры версии 1.0 и версии 2.0

Конечная точка версии 1.0 (используется в ADAL) выдает только маркеры версии 1.0.

Конечная точка версии 2.0 (используемая MSAL) может выдавать маркеры v 1.0 и v 2.0. В манифесте приложения веб-API доступно свойство, которое позволяет разработчикам выбрать допустимую версию маркера. См. `accessTokenAcceptedVersion` в справочной документации по [манифесту приложения](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) .

Дополнительные сведения о маркерах v 1.0 и v 2.0 см. в разделе [Azure Active Directory доступа к маркерам](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

## <a name="adal-to-msal-migration"></a>Переход с ADAL на MSAL

В ADAL4J маркеры обновления были предоставлены, что позволяло разработчикам кэшировать их. Затем они будут использовать `AcquireTokenByRefreshToken()` для предоставления решений, таких как реализация длительно выполняющихся служб, которые обновляют панели мониторинга от имени пользователя, когда пользователь больше не подключен.

MSAL для Java не предоставляет маркеры обновления по соображениям безопасности. Вместо этого MSAL обрабатывает маркеры обновления.

В MSAL для Java имеется API, позволяющий перенести маркеры обновления, полученные с помощью ADAL4j, в Клиентаппликатион: [acquireToken (рефрештокенпараметерс)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-). Этот метод позволяет передавать ранее использованный маркер обновления вместе с любым нужными областями (ресурсами). Маркер обновления будет заменен на новый и кэширован для использования приложением.

В следующем фрагменте кода показан код переноса в конфиденциальном клиентском приложении:

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

`IAuthenticationResult` возвращает маркер доступа и маркер идентификации, а новый маркер обновления хранится в кэше. Теперь приложение также будет содержать Иаккаунт:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Чтобы использовать маркеры, которые теперь находятся в кэше, вызовите:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build(); 
IAuthenticationResult result = app.acquireToken(parameters);
```
