---
title: Настройка регистрации и входиный учет twitter
titleSuffix: Azure AD B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Twitter, используя Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62a283efb93987d3c4a6564c9b25d2031c269559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051470"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Twitter через Azure Active Directory B2C

## <a name="create-an-application"></a>Создание приложения

Чтобы использовать Twitter в качестве поставщика удостоверений в Azure AD B2C, необходимо создать приложение Twitter. Если у вас еще нет учетной записи [https://twitter.com/signup](https://twitter.com/signup)Twitter, вы можете зарегистрироваться в .

1. Выполните вход на [веб-сайт разработчиков Twitter](https://developer.twitter.com/en/apps) с учетными данными для учетной записи Twitter.
1. Выберите **Create an app** (Создать приложение).
1. Введите **имя приложения** и **описание приложения**.
1. В поле **Website URL** (URL-адрес веб-сайта) введите `https://your-tenant.b2clogin.com`. Замените `your-tenant` именем вашего клиента. Например, `https://contosob2c.b2clogin.com`.
1. В поле **Callback URL** (URL-адрес обратного вызова) введите значение `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Замените `your-tenant` именем своего клиента, а `your-user-flow-Id` — идентификатором своего потока пользователя. Например, `b2c_1A_signup_signin_twitter`. При вводе имени клиента и идентификатора потока пользователя необходимо использовать все буквы в нижнем регистре, даже если они определены буквами верхнего регистра в Azure AD B2C.
1. В нижней части страницы прочтите условия использования и примите их, а затем щелкните **Create** (Создать).
1. На странице **сведений о приложении** выберите **Edit > Edit details** (Изменить > Изменить сведения), установите флажок в поле **Enable Sign in with Twitter** (Включить вход через Twitter), а затем нажмите кнопку **Save** (Сохранить).
1. Выберите **Keys and tokens** (Ключи и маркеры), а затем запишите значения **ключа API клиента** и **секретного ключа API клиента**, которые понадобятся далее.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Настройка Twitter в качестве поставщика удостоверений в клиенте

1. Вопийте на [портале Azure](https://portal.azure.com/) в качестве глобального администратора вашего арендатора Azure AD B2C.
1. Убедитесь, что используете каталог с клиентом Azure AD B2C, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
1. Выберите **поставщиков идентификационных данных,** а затем выберите **Twitter**.
1. Введите **имя**. Например, *Twitter*.
1. Для **идентификатора клиента**введите aPI-ключ приложения Twitter, созданного ранее.
1. Для **секрета клиента**введите секретный ключ API Consumer API, который вы записали.
1. Нажмите кнопку **Сохранить**.
