---
title: Передача маркера доступа через поток пользователя в приложение — Azure Active Directory B2C | Документация Майкрософт
description: Сведения о передаче маркера доступа для поставщиков удостоверений OAuth2.0 в качестве утверждения через поток пользователя в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8e9019699d8a81d31d2b20f674fd76fcb70021d6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846822"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Передача маркера доступа с помощью потока пользователя в приложение в Azure Active Directory B2C

> [!NOTE]
> Эта функция сейчас доступна в виде общедоступной предварительной версии.

[Поток пользователя](active-directory-b2c-reference-policies.md) в Azure Active Directory (Azure AD) B2C предоставляет пользователям вашего приложения возможность зарегистрироваться или войти в систему с помощью поставщика удостоверений. При этом Azure AD B2C получает [маркер доступа](active-directory-b2c-reference-tokens.md) от поставщика удостоверений. Azure AD B2C использует этот маркер для извлечения сведений о пользователе. Включите утверждение в свой поток пользователя, чтобы передать маркер через приложения, которые вы регистрируете в Azure AD B2C.

В настоящее время Azure AD B2C поддерживает только передачу маркера доступа поставщиков удостоверений [OAuth 2.0](active-directory-b2c-reference-oauth-code.md), в том числе [Facebook](active-directory-b2c-setup-fb-app.md) и [Google](active-directory-b2c-setup-goog-app.md). Для остальных поставщиков удостоверений утверждение возвращается пустым.

## <a name="prerequisites"></a>Предварительные требования

- Приложение должно использовать [поток пользователя версии 2](user-flow-versions.md).
- Ваш поток пользователя настроен с поставщиком удостоверений OAuth 2.0.

## <a name="enable-the-claim"></a>Включение утверждения

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
2. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. В верхнем меню выберите **каталог и фильтр подписок** и выберите каталог, содержащий ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
4. Выберите **потоки пользователей (политики)** , а затем выберите пользовательский поток. Например, **B2C_1_signupsignin1**.
5. Выберите элемент **Утверждения приложения**.
6. Включите утверждение **маркера доступа поставщика удостоверений** .

    ![Включение утверждения маркера доступа поставщика удостоверений](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Нажмите кнопку **Сохранить**, чтобы сохранить поток пользователя.

## <a name="test-the-user-flow"></a>Тестирование потока пользователя

При тестировании приложений в Azure AD B2C может потребоваться вернуть маркер Azure AD B2C в `https://jwt.ms` для просмотра в нем утверждений.

1. На странице "Обзор" потока пользователя выберите **Выполнить поток пользователя**.
2. В разделе **Приложение** выберите зарегистрированное ранее приложение. Чтобы маркер отображался, как в приведенном ниже примере, **URL-адрес ответа** должен быть следующим: `https://jwt.ms`.
3. Щелкните **Выполнить поток пользователя**, а затем войдите, используя свои учетные данные. Вы должны увидеть маркер доступа поставщика удостоверений в утверждении **idp_access_token**.

    Должен отобразиться результат, аналогичный следующему примеру:

    ![Декодированный токен в jwt.ms с выделенным блоком idp_access_token](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения см. в [обзоре маркеров Azure AD B2C](active-directory-b2c-reference-tokens.md).
