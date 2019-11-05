---
title: Использование AppAuth в приложении iOS в Azure Active Directory B2C | Документация Майкрософт
description: В этой статье описано, как создать приложение iOS, которое использует AppAuth для управления удостоверениями и аутентификации пользователей в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0fb5341c2e7ee55391cb38251b0ea66b55b93301
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469157"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C. Вход с помощью приложения iOS

Платформа Microsoft Identity использует открытые стандарты, такие как OAuth2 и OpenID Connect. При использовании протокола открытого стандарта доступен более широкий выбор библиотек для интеграции со службами. Это и другие подобные руководства предназначены помочь разработчикам в написании приложений, которые подключаются к платформе Microsoft Identity. Большинство библиотек, в которых реализована [спецификация RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749), могут подключаться к платформе Microsoft Identity.

> [!WARNING]
> Корпорация Майкрософт не предоставляет исправления для библиотек сторонних производителей и не выполняла их проверку. В этом примере используется библиотека стороннего производителя с именем AppAuth, которая была протестирована в основных сценариях на совместимость со службой Azure AD B2C. Проблемы и запросы возможностей следует отправлять в проекты с открытым кодом библиотеки. Дополнительные сведения см. в [этой статье](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Если вы еще не работали с OAuth2 или OpenID Connect, вы не поймете большую часть примера конфигурации. Для начала мы рекомендуем просмотреть [общие сведения о протоколе](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Создание каталога Azure AD B2C
Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для всех пользователей, приложений, групп и т. д. Если каталог B2C еще не создан, [создайте его](tutorial-create-tenant.md), прежде чем продолжить.

## <a name="create-an-application"></a>Создание приложения

Затем зарегистрируйте приложение в клиенте Azure AD B2C. Это предоставляет Azure AD сведения, необходимые для безопасного обмена данными с приложением.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Запишите **идентификатор приложения (клиента)** для использования на более позднем этапе.

Кроме того, запишите пользовательский URI перенаправления для использования на следующем шаге. Пример: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Создание потоков пользователей
В Azure AD B2C любое взаимодействие с пользователем определяется [потоком пользователя](active-directory-b2c-reference-policies.md). Это приложение предусматривает одну процедуру идентификации, сочетающую в себе вход и регистрацию. При создании потока пользователя обязательно сделайте следующее:

* В разделе **Sign-up attributes** (Атрибуты регистрации) выберите атрибут **Отображаемое имя**.  Можно также выбрать другие атрибуты.
* В разделе **Application claims** (Утверждения приложения) выберите утверждения **Отображаемое имя** и **ИД объекта пользователя**. Можно также выбрать другие утверждения.
* Скопируйте **имя** каждого потока пользователя после его создания. При сохранении потока пользователя к его имени добавляется префикс `b2c_1_`.  Имя потока пользователя понадобится вам позже.

Создав потоки пользователей, можно приступать к сборке приложения.

## <a name="download-the-sample-code"></a>Скачивание примера кода
Мы разместили рабочий пример, использующий AppAuth с Azure AD B2C, [на сайте GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Вы можете скачать код и запустить его. Чтобы использовать собственный клиент Azure AD B2C, следуйте инструкциям в файле [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Этот пример был создан по инструкциям в файле README [проекта AppAuth для iOS на сайте GitHub](https://github.com/openid/AppAuth-iOS). Чтобы получить дополнительные сведения о работе этого примера и библиотеки, обратитесь к файлу README для AppAuth на сайте GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Изменение приложения для использования Azure AD B2C с AppAuth

> [!NOTE]
> AppAuth поддерживает iOS 7 и более поздние версии.  Однако для поддержки социальных имен входа в Google необходим SFSafariViewController, который поддерживает iOS 9 и более поздние версии.
>

### <a name="configuration"></a>Конфигурация

Вы можете настроить взаимодействие с Azure AD B2C, указав URI конечной точки авторизации и конечной точки токена.  Для создания URI вам потребуются следующие сведения:
* идентификатор клиента (например, contoso.onmicrosoft.com);
* имя потока пользователя (например, B2C\_1\_SignUpIn).

URI конечной точки токена можно создать, заменив Tenant\_ID (идентификатор клиента) и Policy\_Name (имя политики) в следующем URL-адресе:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

URI конечной точки авторизации можно создать, заменив Tenant\_ID (идентификатор клиента) и Policy\_Name (имя политики) в следующем URL-адресе:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Выполните следующий код для создания объекта AuthorizationServiceConfiguration:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Авторизация

После настройки или извлечения конфигурации службы авторизации можно сформировать запрос авторизации. Для создания запроса вам потребуются следующие сведения:

* Идентификатор клиента (идентификатор приложения), записанный ранее. Пример: `00000000-0000-0000-0000-000000000000`.
* Пользовательский URI перенаправления, записанный ранее. Пример: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Оба элемента нужно сохранить при [регистрации приложения](#create-an-application).

```objc
OIDAuthorizationRequest *request =
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow =
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Чтобы настроить приложение для обработки перенаправления к URI с настраиваемой схемой, необходимо обновить список схем URL-адреса в файле Info.pList:
* Откройте файл Info.pList.
* Наведите указатель мыши на строку, например "Код типа ОС для пакета", и щелкните символ \+.
* Переименуйте новую строку на "Типы URL-адреса".
* Щелкните стрелку слева от элемента "Типы URL-адреса", чтобы открыть дерево.
* Щелкните стрелку слева от элемента "Элемент 0", чтобы открыть дерево.
* Переименуйте первый элемент под элементом 0 в "Схемы URL-адресов".
* Щелкните стрелку слева от элемента "Схемы URL-адресов", чтобы открыть дерево.
* В столбце "Значение" слева от элемента "Элемент 0", под элементом "Схемы URL-адресов", находится пустое поле.  Присвойте ему значение уникальной схемы своего приложения.  Оно должно соответствовать схеме, указанной в redirectURL при создании объекта OIDAuthorizationRequest.  В этом примере используется схема com.onmicrosoft.fabrikamb2c.exampleapp.

Сведения о завершении остальной части процесса см. в [руководстве по AppAuth](https://openid.github.io/AppAuth-iOS/). Если вам нужно быстро приступить к работе с приложением, ознакомьтесь с [этим примером](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Следуйте указаниям в файле [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md), чтобы ввести собственные значения для настройки Azure AD B2C.

Мы всегда рады вашим отзывам и предложениям! Если у вас возникли трудности с выполнением действий, описанных в этой статье или у вас есть рекомендации по улучшению этого материала, поделитесь с нами своими наблюдениями, воспользовавшись формой внизу страницы. Запросы функций оставляйте на форуме [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
