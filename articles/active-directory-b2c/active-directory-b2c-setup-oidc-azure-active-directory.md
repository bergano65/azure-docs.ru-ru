---
title: Настройка входа для Azure Active Directory организации Azure Active Directory B2C
description: Настройка входа для определенной организации Azure Active Directory в Azure Active Directory B2C
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 0c2e368b9c12d8ab673e5b8808632501de448b9a
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755768"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Настройка входа для определенной организации Azure Active Directory в Azure Active Directory B2C

Чтобы использовать Azure Active Directory (Azure AD) в качестве [поставщика удостоверений](active-directory-b2c-reference-oauth-code.md) для Azure AD B2C, необходимо создать приложение, которое будет представлять этого поставщика. В этой статье описывается, как включить вход для пользователей из определенной организации Azure AD с помощью потока пользователя в Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Создание приложения Azure AD

Чтобы включить вход для пользователей из определенной организации Azure AD, вам необходимо зарегистрировать приложение в клиенте Azure AD этой организации, а не в вашем клиенте Azure AD B2C.

1. Войдите на [портале Azure](https://portal.azure.com).
2. Убедитесь, что вы используете каталог, содержащий клиент Azure AD. В верхнем меню выберите фильтр **каталог и подписка** и выберите каталог, содержащий ваш клиент Azure AD. Это не тот же клиент, что и Azure AD B2C клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
4. Выберите **Новая регистрация**.
5. Введите имя приложения. Пример: `Azure AD B2C App`.
6. Примите выбранные **учетные записи в этом каталоге организации только** для этого приложения.
7. В качестве **URI перенаправления**оставьте значение **Web**и введите следующий URL-адрес в строчных буквах, где `your-B2C-tenant-name` заменяется именем клиента Azure AD B2C. Например, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Всем URL-адресам следует использовать [b2clogin.com](b2clogin.md).

8. Щелкните **Зарегистрировать**. Скопируйте **идентификатор приложения (клиента)** , который будет использоваться позже.
9. Выберите **сертификаты & секреты** в меню приложение, а затем выберите **новый секрет клиента**.
10. Введите имя секрета клиента. Пример: `Azure AD B2C App Secret`.
11. Выберите срок действия. Для этого приложения Примите выбор **в течение 1 года**.
12. Выберите **Добавить** и скопируйте значение нового секрета клиента, который будет использоваться позже.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Настройка Azure AD в качестве поставщика удостоверений для клиента

1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C. В верхнем меню выберите фильтр **каталог и подписка** и выберите каталог, содержащий клиент Azure AD B2C.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. Выберите **поставщики удостоверений**, а затем выберите **Новый поставщик OpenID Connect Connect**.
1. Введите **Имя**. Например, введите *Azure AD Contoso*.
1. В поле **URL-адрес метаданных**введите следующий URL-адрес, заменяющий `your-AD-tenant-domain` именем домена клиента Azure AD:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Пример: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

    **Не** используйте конечную точку МЕТАДАННЫХ Azure AD версии 2.0, например `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`. Это приведет к ошибке, похожей на `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` при попытке входа.

1. В поле **идентификатор клиента**введите ранее записанный идентификатор приложения.
1. В поле **секрет клиента**введите секрет клиента, который вы записали ранее.
1. Оставьте значения по умолчанию для **область**, **тип**отклика и **режим ответа**.
1. Используемых Введите значение для **Domain_hint**. Например, *контосоад*. Это значение используется при ссылке на этого поставщика удостоверений с помощью *domain_hint* в запросе.
1. В разделе **сопоставление утверждений поставщика удостоверений**введите следующие значения сопоставления утверждений.

    * **Идентификатор пользователя**: *OID*
    * **Отображаемое имя**: *имя*
    * **Заданное имя**: *given_name*
    * **Фамилия**: *family_name*
    * **Адрес электронной почты**: *unique_name*

1. Щелкните **Сохранить**.
