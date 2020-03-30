---
title: Отвагайте проверку электронной почты во время регистрации клиента с пользовательской политикой
titleSuffix: Azure AD B2C
description: Узнайте, как отключить проверку электронной почты во время регистрации клиента в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 13a5fa6a030d876d92651ca587e37fdc6a3ec600
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136148"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Отработайте проверку электронной почты во время регистрации клиента с помощью пользовательской политики в Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Предварительные требования

Выполните шаги, описанные в статье [Начало работы с настраиваемыми политиками в Azure Active Directory B2C](custom-policy-get-started.md). Вы должны иметь рабочую таможенную политику для регистрации и регистрации с социальными и местными учетными записями.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Добавление метаданных в самоутверждаемый технический профиль

Технический профиль **LocalAccountSignUpWithLogonEmail** является [самоутверждаемым,](self-asserted-technical-profile.md)который вызывается во время потока регистрации. Чтобы отключить проверку электронной `EnforceEmailVerification` почты, установите метаданные на ложные. Переиздайте технические профили LocalAccountSignUpWithLogonEmail в файле расширения. 

1. Откройте файл расширений вашей политики. Например, <em> `SocialAndLocalAccounts/` </em>.
1. Найдите элемент `ClaimsProviders`. Если такой элемент не существует, добавьте его.
1. Добавьте в элемент `ClaimsProviders` следующего поставщика претензий:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>Проверка пользовательской политики

1. Войдите на [портал Azure](https://portal.azure.com).
2. Убедитесь, что вы используете каталог, содержащий ваш клиент Azure AD, выбрав фильтр **подписки каталога в** верхнем меню и выбрав каталог, содержащий арендатор Azure AD.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
4. Выберите **Инфраструктура процедур идентификации**.
5. Выберите **Отправка пользовательской политики** и отправьте два файла политики, которые вы изменили ранее.
2. Выберите отправленную вами политику регистрации или входа и нажмите кнопку **Выполнить**.
3. Вы должны иметь возможность зарегистрироваться с помощью адреса электронной почты без проверки.


## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [самоутвержденном техническом профиле](self-asserted-technical-profile.md) в справке IEF.
