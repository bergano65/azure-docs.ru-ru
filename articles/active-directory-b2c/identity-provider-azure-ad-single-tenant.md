---
title: Настройка регистрации для организации Azure AD
titleSuffix: Azure AD B2C
description: Настройка входа для определенной организации Azure Active Directory в Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 35fc4e1d64fa7df392fa878db14c0464da7dccf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188313"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Настройка входа для определенной организации Azure Active Directory в Azure Active Directory B2C

Чтобы использовать Azure Active Directory (Azure AD) в качестве [поставщика удостоверений](authorization-code-flow.md) для Azure AD B2C, необходимо создать приложение, которое будет представлять этого поставщика. В этой статье описывается, как включить вход для пользователей из определенной организации Azure AD с помощью потока пользователя в Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Создание приложения Azure AD

Чтобы включить вход для пользователей из определенной организации Azure AD, вам необходимо зарегистрировать приложение в клиенте Azure AD этой организации, а не в вашем клиенте Azure AD B2C.

1. Войдите на [портал Azure](https://portal.azure.com).
2. Убедитесь, что вы используете каталог, содержащий ваш арендатор Azure AD. Выберите фильтр **подписки каталога** в верхнем меню и выберите каталог, содержащий ваш арендатор Azure AD. Это не тот же арендатор, что и ваш арендатор Azure AD B2C.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Регистрация приложений**.
4. Выберите **Новая регистрация**.
5. Введите имя приложения. Например, `Azure AD B2C App`.
6. Примите выбор **учетных записей в этом каталоге организации только** для этого приложения.
7. Для **перенаправления URI**примите значение **Web**и введите следующий URL `your-B2C-tenant-name` во всех буквах нижнего регистра, где он заменяется именем вашего арендатора Azure AD B2C. Например, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Всем URL-адресам следует использовать [b2clogin.com](b2clogin.md).

8. Нажмите кнопку **Зарегистрировать**. Копирование **идентификатора приложения (клиента),** которое будет использоваться позже.
9. Выберите **Сертификаты & секреты** в меню приложения, а затем выберите **новый секрет клиента.**
10. Введите имя для секрета клиента. Например, `Azure AD B2C App Secret`.
11. Выберите период истечения срока действия. Для этого приложения, принять выбор **В 1 год**.
12. Выберите **Добавить** и скопировать значение нового клиента секрет, который отображается для использования позже.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Настройка Azure AD в качестве поставщика удостоверений для клиента

1. Убедитесь, что вы используете каталог, содержащий арендатор Azure AD B2C. Выберите фильтр **подписки каталога** в верхнем меню и выберите каталог, содержащий ваш клиент Azure AD B2C.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, а затем найдите и выберите **Azure AD B2C**.
1. Выберите **поставщиков идентификационных данных,** а затем выберите **нового поставщика OpenID Connect.**
1. Введите **имя**. Например, введите *Azure AD Contoso*.
1. Для **URL-адреса Metadata**введите следующий URL-адрес, заменив `your-AD-tenant-domain` его доменным именем вашего aure AD-арендатора:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Например, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

    **Не** используйте конечную точку метаданных Azure AD `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`v2.0, например. Это приводит к ошибке, аналогичной `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` при попытке войти в систему.

1. Для **идентификатора клиента**введите идентификатор приложения, который вы ранее записали.
1. Для **секрета клиента,** введите секрет клиента, который вы ранее записали.
1. Оставьте значения по умолчанию для **scope,** **Response type**и **режима ответа.**
1. (Необязательно) Введите значение для **Domain_hint.** Например, *ContosoAD*. Это значение используется при ссылке на этого поставщика удостоверений с помощью *domain_hint* в запросе.
1. В соответствии с **картированием требований поставщика идентификационных данных**введите следующие значения отображения утверждений:

    * **Идентификатор пользователя**: *oid*
    * **Имя дисплея**: *имя*
    * **Данное имя**: *given_name*
    * **Фамилия**: *family_name*
    * **Электронная почта**: *unique_name*

1. Нажмите кнопку **Сохранить**.
