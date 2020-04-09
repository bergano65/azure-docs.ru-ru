---
title: ADAL к MSAL руководство по миграции (MSAL4j) Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как перенести приложение Azure Active Directory Authentication Library (ADAL) Java в Библиотеку подлинности Майкрософт (MSAL).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: 2929b94a2cb624b96649292714fe93dea09a2085
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886506"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>ADAL для руководства по миграции MSAL для Java

В этой статье освещаются изменения, необходимые для переноса приложения, использующее Библиотеку подлинности активных каталогов Azure (ADAL) для использования библиотеки подлинности Майкрософт (MSAL).

Библиотека аутентификации Microsoft для Java (MSAL4J) и Библиотека аутентификации Azure AD для Java (ADAL4J) используются для аутентификации объектов Azure AD и запроса токенов из Azure AD. До сих пор большинство разработчиков работали с Azure AD для платформы разработчиков (v1.0) для проверки подлинности идентификаторов Azure AD (рабочие и школьные учетные записи) путем запроса токенов с помощью Azure AD Authentication Library (ADAL).

MSAL предлагает следующие преимущества:

- Поскольку в ней используется новая конечная точка платформы майкрософт, можно проверить подлинность более широкого набора идентификаторов Microsoft, таких как идентификаторы AD Azure, учетные записи Майкрософт, а также социальные и локальные учетные записи через Azure AD Business to Consumer (B2C).
- Ваши пользователи получат лучший опыт с одним подписанием.
- Приложение может включать постепенное согласие, а поддержка условного доступа проще.

MSAL для Java — это библиотека auth, который мы рекомендуем вам использовать с платформой идентификации Майкрософт. Новые функции на ADAL4J не будут реализованы. Все усилия в будущем направлены на улучшение MSAL.

## <a name="differences"></a>Различия

Если вы работали с Azure AD для разработчиков (v1.0) конечная точка (и ADAL4J), вы можете прочитать [Что отличается от платформы идентификации Microsoft (v2.0) конечная точка?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison).

## <a name="scopes-not-resources"></a>Области вместо ресурсов

ADAL4J приобретает токены для ресурсов, в то время как MSAL для Java приобретает токены для областей. Ряд MSAL для классов Java требует параметра областей. Этот параметр представляет собой список строк, которые объявляют желаемые разрешения и ресурсы, которые запрашиваются. Просмотрите [области Microsoft Graph,](https://docs.microsoft.com/graph/permissions-reference) чтобы увидеть примеры областей.

## <a name="core-classes"></a>Основные классы

В ADAL4J `AuthenticationContext` класс представляет подключение к службе безопасности (STS) или серверу авторизации через орган. Тем не менее, MSAL для Java разработан вокруг клиентских приложений. Он предусматривает два `PublicClientApplication` отдельных класса: и `ConfidentialClientApplication` представлять клиентские приложения.  Последний, `ConfidentialClientApplication`представляет собой приложение, которое предназначено для надежного сохранения секрета, например, идентификатор приложения для приложения daemon.

В следующей таблице показано, как ADAL4J выполняет карту новых функций MSAL для Java:

| Метод ADAL4J| Метод MSAL4J|
|------|-------|
|acquireToken (строка ресурса, ClientCredential учетных данных, аутентификацияCallback обратный вызов) | acquireToken (ClientCredentialПараметры)|
|acquireToken (строка ресурса, ClientAssertion утверждение, AuthenticationCallback обратный вызов)|acquireToken (ClientCredentialПараметры)|
|acquireToken (струнный ресурс, AsymmetricKeyCredential, аутентификацияCallback обратный вызов)|acquireToken (ClientCredentialПараметры)|
|acquireToken (строка ресурса, строка clientId, Строка имя пользователя, строка пароль, аутентификацияCallback обратный вызов)| acquireToken (UsernamePasswordParameters)|
|acquireToken (строка ресурса, Строка clientId, Строка имя пользователя, Строка password'null, АутентификацияCallback обратный вызов)|acquireToken (ИнтегрированныеПараметрыВазавальные Параметры)|
|acquireToken (строка ресурса, UserAssertion userAssertion, Клиентcredential учетные данные, аутентификацияCallback обратный вызов)| acquireToken (OnBehalfOfПараметры)|
|приобрестиTokenByАвторивационный код() | acquireToken (АвторизацияПараметры) |
| приобрестиDeviceCode () и приобрестиTokenByDeviceCode()| acquireToken (Параметры устройства)|
|acquireTokenByrefreshtoken()| acquireTokenSilently (Silent Parameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount вместо IUser

ADAL4J манипулировал пользователями. Хотя пользователь представляет одного человека или программного агента, он может иметь одну или несколько учетных записей в системе идентификации Майкрософт. Например, у пользователя может быть несколько личных учетных записей Azure AD, Azure AD B2C или личные учетные записи Майкрософт.

MSAL для Java определяет концепцию `IAccount` учетной записи через интерфейс. Это нарушение от ADAL4J, но оно является хорошим, поскольку оно отражает тот факт, что один и тот же пользователь может иметь несколько учетных записей, и, возможно, даже в различных каталогах Azure AD. MSAL для Java предоставляет более подробную информацию в сценариях гостей, поскольку предоставляется информация о домашней учетной записи.

## <a name="cache-persistence"></a>Сохраняемость кэша

ADAL4J не имел поддержки кэша токенов.
MSAL для Java добавляет [кэш маркеров](msal-acquire-cache-tokens.md) для упрощения управления сроками службы токенов путем автоматического обновления токенов с истекшим сроком действия и предотвращения ненужных запросов для пользователя, чтобы предоставить учетные данные, когда это возможно.

## <a name="common-authority"></a>Общий орган власти

В v1.0, если `https://login.microsoftonline.com/common` вы используете авторитет, пользователи могут войти в любую учетную запись Azure Active Directory (AAD) (для любой организации).

Если вы `https://login.microsoftonline.com/common` используете полномочия в v2.0, пользователи могут войти в любую организацию AAD или даже личный аккаунт Майкрософт (MSA). В MSAL для Java, если вы хотите ограничить вход в любую учетную запись AAD, необходимо использовать `https://login.microsoftonline.com/organizations` авторитет (который является таким же поведением, как с ADAL4J). Чтобы указать орган, `authority` установите параметр в методе [PublicClientApplication.Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) при создании `PublicClientApplication` класса.

## <a name="v10-and-v20-tokens"></a>Маркеры версии 1.0 и версии 2.0

Конечная точка версии 1.0 (используется в ADAL) выдает только маркеры версии 1.0.

Конечная точка v2.0 (используется MSAL) может излучать токены v1.0 и v2.0. В манифесте приложения веб-API доступно свойство, которое позволяет разработчикам выбрать допустимую версию маркера. Смотрите `accessTokenAcceptedVersion` в [заявке манифест](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) справочной документации.

Для получения дополнительной информации о токенах v1.0 и v2.0 [см.](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)

## <a name="adal-to-msal-migration"></a>Переход с ADAL на MSAL

В ADAL4J токены обновления были выставлены, что позволило разработчикам кэшировать их. Затем они `AcquireTokenByRefreshToken()` будут использоваться для включения таких решений, как реализация долгосрочных служб, которые обновляют панели мониторинга от имени пользователя, когда пользователь больше не подключен.

MSAL для Java не предоставляет маркеры обновления по соображениям безопасности. Вместо этого MSAL обрабатывает освежающие токены для вас.

MSAL для Java имеет API, который позволяет перенести токены обновления, приобретенные с помощью ADAL4j, в клиентское приложение: [приобрестиToken (RefreshTokenParameters).](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-) Этот метод позволяет передавать ранее использованный маркер обновления вместе с любым нужными областями (ресурсами). Токен обновления будет обменен на новый и кэширован для использования вашим приложением.

Следующий фрагмент кода показывает некоторый миграционный код в конфиденциальном клиентском приложении:

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

Возвращает `IAuthenticationResult` токен доступа и токен идентификатор, в то время как новый маркер обновления хранится в кэше. Приложение также теперь будет содержать IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Чтобы использовать токены, которые сейчас находятся в кэше, позвоните по телефону:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build(); 
IAuthenticationResult result = app.acquireToken(parameters);
```
