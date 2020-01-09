---
title: Регистрация и вход с помощью настраиваемых политик для телефона
titleSuffix: Azure AD B2C
description: Узнайте, как отправить одноразовые пароли в текстовых сообщениях для телефонов пользователей приложения с пользовательскими политиками в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 96bf088e6db29817d1ae4276c254a84723e5d03d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480194"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>Настройка регистрации телефона и входа в систему с помощью пользовательских политик в Azure AD B2C

Регистрация и вход в систему в Azure Active Directory B2C (Azure AD B2C) позволяет пользователям регистрироваться и входить в приложения с помощью одноразового пароля (OTP), отправленного в текстовом сообщении на телефон. Одноразовые пароли могут помочь в минимальном риске пользователей, которые забывают или нарушают свой пароль.

Выполните действия, описанные в этой статье, чтобы использовать пользовательские политики, чтобы пользователи могли регистрироваться и входить в приложения с помощью одноразового пароля, отправленного на свой телефон.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Технические условия

* [клиент Azure AD B2C](tutorial-create-tenant.md);
* [Веб-приложение, зарегистрированное](tutorial-register-applications.md) в клиенте
* [Пользовательские политики](active-directory-b2c-get-started-custom.md) , отправленные в клиент

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Получение телефонного входа & начальный пакет входа в систему

Начните с обновления файлов настраиваемой политики регистрации и входа в телефон для работы с вашим клиентом Azure AD B2C.

В следующих шагах предполагается, что вы выполнили [необходимые условия](#prerequisites) и уже создали клонированный репозиторий [начального пакета пользовательской политики][starter-pack] на локальном компьютере.

1. Найдите [файлы настраиваемой политики регистрации и входа для телефона][starter-pack-phone] в локальном клоне репозитория начального пакета или скачайте их напрямую. Файлы политики XML находятся в следующем каталоге:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. В каждом файле замените строку `yourtenant` именем клиента Azure AD B2C. Например, если имя клиента B2C — *contosob2c*, все экземпляры `yourtenant.onmicrosoft.com` становятся `contosob2c.onmicrosoft.com`.

1. Выполните действия, описанные в разделе [Добавление идентификаторов приложений в пользовательскую политику](active-directory-b2c-get-started-custom.md#add-application-ids-to-the-custom-policy) статьи [Приступая к работе с пользовательскими политиками в Azure Active Directory B2C](active-directory-b2c-get-started-custom.md). В этом случае обновите `/phone-number-passwordless/` **`Phone_Email_Base.xml`** с **идентификаторами приложения (клиента)** двух приложений, зарегистрированных при выполнении необходимых условий, *IdentityExperienceFramework* и *ProxyIdentityExperienceFramework*.

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
1. В качестве **URL-адреса ответа выберите**`https://jwt.ms`.
1. Выберите **Запустить сейчас** и зарегистрируйтесь, используя адрес электронной почты или номер телефона.
1. Выберите **выполнить** еще раз и войдите с помощью той же учетной записи, чтобы подтвердить правильность конфигурации.

## <a name="next-steps"></a>Дальнейшие действия

Начальный пакет пользовательской политики регистрации и входа в систему можно найти на сайте GitHub:

[Azure-Samples/Active-Directory-B2C-Custom-Policy-starterpack/сценарии/телефон — номер для пароля][starter-pack-phone]

В файлах политики начального пакета используются технические профили многофакторной идентификации и преобразования заявок на телефонные номера:

* [Определение технического профиля многофакторной идентификации Azure](multi-factor-auth-technical-profile.md)
* [Определение преобразований заявок на телефонный номер](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
