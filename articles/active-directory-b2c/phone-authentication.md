---
title: Регистрация по телефону и регистрация с пользовательскими политиками (Предварительный просмотр)
titleSuffix: Azure AD B2C
description: Отправка одноразовых паролей (OTP) в текстовых сообщениях на телефоны пользователей приложения с пользовательскими политиками в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eadac0e973b361b1fdee63dcc9cfa848a0b2bacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183964"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Настройка регистрации телефона и всасывайте в пользовательские политики в Azure AD B2C (Предварительный просмотр)

Регистрация по телефону и всасывание в Azure Active Directory B2C (Azure AD B2C) позволяет пользователям зарегистрироваться и войти в приложения с помощью одноразового пароля (OTP), отправленного в текстовом сообщении на свой телефон. Одноразовые пароли могут помочь свести к минимуму риск забвения пользователей или компрометации их паролей.

Выполните действия в этой статье, чтобы использовать пользовательские политики, чтобы ваши клиенты могли зарегистрироваться и войти в приложения, используя одноразовый пароль, отправленный на их телефон.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>Цены

Одноразовые пароли отправляются пользователям с помощью SMS-сообщений, и с вас может взиматься плата за каждое отправленное сообщение. Для получения информации о ценах смотрите раздел **«Отдельные сборы»** [цены Azure Active Directory B2C.](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

## <a name="prerequisites"></a>Предварительные требования

Перед настройкой OTP необходимы следующие ресурсы.

* [Клиент Azure AD B2C](tutorial-create-tenant.md)
* [Веб-приложение, зарегистрированное](tutorial-register-applications.md) в вашем арендаторе
* [Пользовательские политики,](custom-policy-get-started.md) загруженные арендатору

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Получить телефон регистрации & входе в стартовый пакет

Начните с обновления регистрации телефона и входе в пользовательские файлы политики для работы с вашим арендатором Azure AD B2C.

Следующие шаги предполагают, что вы выполнили [предпосылки](#prerequisites) и уже клонировали репозиторий [пользовательского пакета для запуска политики][starter-pack] для локальной машины.

1. Найдите [файлы пользовательского полиса для регистрации телефона и ввешие в пользовательские файлы политики][starter-pack-phone] в локальном клоне репо стартового пакета или загрузите их напрямую. Файлы политики XML расположены в следующем каталоге:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. В каждом файле `yourtenant` замените строку именем вашего арендатора Azure AD B2C. Например, если имя вашего арендатора B2C *contosob2c,* все экземпляры `yourtenant.onmicrosoft.com` стали `contosob2c.onmicrosoft.com`.

1. Заполните шаги в [идотах приложения Add в](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) разделе пользовательских политик Get started с [пользовательскими политиками в Azure Active Directory B2C.](custom-policy-get-started.md) В этом случае `/phone-number-passwordless/` **`Phone_Email_Base.xml`** обновляйте **идентификаторы приложений (клиента)** двух приложений, которые вы зарегистрировали при выполнении предварительных условий, *IdentityExperienceFramework* и *ProxyIdentityExperienceFramework.*

## <a name="upload-the-policy-files"></a>Загрузка файлов политик

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к своему арендатору Azure AD B2C.
1. В соответствии с **политиками**выберите **рамки опыта идентификации.**
1. Выберите **пользовательскую политику загрузки.**
1. Загрузите файлы полисов в следующем порядке:
    1. *Phone_Email_Base.xml*
    1. *SignuporSigninWithPhone.xml*
    1. *SignuporSigninWithPhoneorEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

При загрузке каждого файла Azure `B2C_1A_`добавляет префикс.

## <a name="test-the-custom-policy"></a>Проверка пользовательской политики

1. В соответствии с **пользовательскими политиками,** выберите **B2C_1A_SignUpOrSignInWithPhone**.
1. В **приложении Select**выберите приложение *webapp1,* которое вы зарегистрировали при выполнении предварительных условий.
1. Для **выбора URL-адреса ответа**выберите `https://jwt.ms`.
1. Выберите **Выполнить сейчас** и зарегистрироваться, используя адрес электронной почты или номер телефона.
1. Выберите **Run теперь** еще раз и войти в систему с той же учетной записью, чтобы подтвердить, что у вас есть правильная конфигурация.

## <a name="get-user-account-by-phone-number"></a>Получить учетную запись пользователя по номеру телефона

Пользователь, который регистрируется с номером телефона, но не предоставляет адрес электронной почты восстановления, записывается в каталог Azure AD B2C с номером телефона в качестве имени. Если пользователь хочет изменить свой номер телефона, ваша служба поддержки или служба поддержки должны сначала найти свою учетную запись, а затем обновить свой номер телефона.

Вы можете найти пользователя по номеру телефона (имя в ввоза) с помощью [Microsoft Graph:](manage-user-accounts-graph-api.md)

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Пример:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Дальнейшие действия

Вы можете найти телефон регистрации и регистрации пользовательских политики стартовый пакет (и другие стартовые пакеты) на GitHub:

[Azure-Образцы/активный-каталог-b2c-обычай-политика-starterpack/сценарии/телефон-номер-пароль-less][starter-pack-phone]

В файлах политики starter Pack используются многофакторные технические профили аутентификации и преобразования требований по номеру телефона:

* [Определение технического профиля многофакторной аутентификации Azure](multi-factor-auth-technical-profile.md)
* [Определение преобразований требований номера телефона](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
