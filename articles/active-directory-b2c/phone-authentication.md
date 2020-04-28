---
title: Регистрация и вход с помощью настраиваемых политик (Предварительная версия)
titleSuffix: Azure AD B2C
description: Отправка одноразовых паролей (OTP) в текстовых сообщениях на телефоны пользователей приложения с помощью пользовательских политик в Azure Active Directory B2C.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183964"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Настройка регистрации и входа в систему с помощью настраиваемых политик в Azure AD B2C (Предварительная версия)

Регистрация и вход в систему в Azure Active Directory B2C (Azure AD B2C) позволяет пользователям регистрироваться и входить в приложения с помощью одноразового пароля (OTP), отправленного в текстовом сообщении на телефон. Одноразовые пароли могут помочь в минимальном риске пользователей, которые забывают или нарушают свой пароль.

Выполните действия, описанные в этой статье, чтобы использовать пользовательские политики, чтобы пользователи могли регистрироваться и входить в приложения с помощью одноразового пароля, отправленного на свой телефон.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>Цены

Одноразовые пароли отправляются пользователям с помощью SMS-сообщений, и вам может потребоваться оплатить каждое отправленное сообщение. Сведения о ценах см. в разделе " **Специальные расходы** " статьи [Azure Active Directory B2C цены](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="prerequisites"></a>Предварительные требования

Перед настройкой OTP вам потребуются следующие ресурсы.

* [клиент Azure AD B2C](tutorial-create-tenant.md);
* [Веб-приложение, зарегистрированное](tutorial-register-applications.md) в клиенте
* [Пользовательские политики](custom-policy-get-started.md) , отправленные в клиент

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Получение телефонного входа & начальный пакет входа в систему

Начните с обновления файлов настраиваемой политики регистрации и входа в телефон для работы с вашим клиентом Azure AD B2C.

В следующих шагах предполагается, что вы выполнили [необходимые условия](#prerequisites) и уже создали клонированный репозиторий [начального пакета пользовательской политики][starter-pack] на локальном компьютере.

1. Найдите [файлы настраиваемой политики регистрации и входа для телефона][starter-pack-phone] в локальном клоне репозитория начального пакета или скачайте их напрямую. Файлы политики XML находятся в следующем каталоге:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. В каждом файле замените строку `yourtenant` именем клиента Azure AD B2C. Например, если имя клиента B2C — *contosob2c*, все экземпляры `yourtenant.onmicrosoft.com` станут. `contosob2c.onmicrosoft.com`

1. Выполните действия, описанные в разделе [Добавление идентификаторов приложений в пользовательскую политику](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) статьи [Приступая к работе с пользовательскими политиками в Azure Active Directory B2C](custom-policy-get-started.md). В этом случае обновите `/phone-number-passwordless/` **`Phone_Email_Base.xml`** **идентификаторы приложения (клиента)** двух приложений, зарегистрированных при выполнении необходимых условий, *IdentityExperienceFramework* и *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Отправка файлов политики

1. Войдите в [портал Azure](https://portal.azure.com) и перейдите к своему клиенту Azure AD B2C.
1. В разделе **политики**выберите **инфраструктура процедур идентификации**.
1. Выберите **Отправить настраиваемую политику**.
1. Отправьте файлы политики в следующем порядке:
    1. *Phone_Email_Base. XML*
    1. *Сигнупорсигнинвисфоне. XML*
    1. *Сигнупорсигнинвисфонеоремаил. XML*
    1. *Профилидитфонеонли. XML*
    1. *Профилидитфонимаил. XML*
    1. *Чанжефоненумбер. XML*
    1. *Пассвордресетемаил. XML*

При передаче каждого файла Azure добавляет префикс `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Проверка пользовательской политики

1. В разделе **пользовательские политики**выберите **B2C_1A_SignUpOrSignInWithPhone**.
1. В разделе **Выбор приложения**выберите приложение *APP1* , которое вы зарегистрировали при выполнении необходимых условий.
1. В качестве **URL-адреса ответа выберите** `https://jwt.ms`.
1. Выберите **Запустить сейчас** и зарегистрируйтесь, используя адрес электронной почты или номер телефона.
1. Выберите **выполнить** еще раз и войдите с помощью той же учетной записи, чтобы подтвердить правильность конфигурации.

## <a name="get-user-account-by-phone-number"></a>Получение учетной записи пользователя по номеру телефона

Пользователь, который регистрируется с номером телефона, но не предоставляет адрес электронной почты для восстановления, записывается в Azure AD B2C каталог с номером телефона в качестве имени для входа. Если пользователь хочет изменить свой номер телефона, ваша служба технической поддержки или специалисты по технической поддержке должны сначала найти свою учетную запись, а затем обновить свой номер телефона.

Вы можете найти пользователя по номеру телефона (имени для входа) с помощью [Microsoft Graph](manage-user-accounts-graph-api.md):

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Пример:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Дальнейшие действия

Начальный пакет пользовательской политики регистрации и входа в систему можно найти на сайте GitHub:

[Azure-Samples/Active-Directory-B2C-Custom-Policy-starterpack/сценарии/телефон — номер для пароля][starter-pack-phone]

В файлах политики начального пакета используются технические профили многофакторной идентификации и преобразования заявок на телефонные номера:

* [Определение технического профиля многофакторной идентификации Azure](multi-factor-auth-technical-profile.md)
* [Определение преобразований заявок на телефонный номер](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
