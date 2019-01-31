---
title: Настройка регистрации и входа с учетной записью Twitter через Azure Active Directory B2C | Документация Майкрософт
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Twitter, используя Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 18a8055e85d69cb956cebb63a0ad0a3680219ef1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165869"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Twitter через Azure Active Directory B2C

## <a name="create-an-application"></a>Создание приложения

Чтобы использовать Twitter в качестве поставщика удостоверений в Azure AD B2C, необходимо создать приложение Twitter. Если у вас нет учетной записи Twitter, вы можете получить ее по адресу [https://twitter.com/signup](https://twitter.com/signup).

1. Выполните вход на [веб-сайт разработчиков Twitter](https://developer.twitter.com/en/apps) с учетными данными для учетной записи Twitter.
2. Выберите **Create an app** (Создать приложение).
3. Введите **имя приложения** и **описание приложения**.
4. В поле **Website URL** (URL-адрес веб-сайта) введите `https://your-tenant.b2clogin.com`. Замените `your-tenant` именем вашего клиента. Например, https://contosob2c.b2clogin.com.
5. В поле **Callback URL** (URL-адрес обратного вызова) введите значение `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Замените `your-tenant` именем своего клиента, а `your-user-flow-Id` — идентификатором своего потока пользователя. Например, `b2c_1A_signup_signin_twitter`. При вводе имени вашего клиента необходимо использовать только строчные буквы, даже если в Azure AD B2C имя клиента определено с прописными буквами.
6. В нижней части страницы прочтите условия использования и примите их, а затем щелкните **Create** (Создать).
7. На странице **сведений о приложении** выберите **Edit > Edit details** (Изменить > Изменить сведения), установите флажок в поле **Enable Sign in with Twitter** (Включить вход через Twitter), а затем нажмите кнопку **Save** (Сохранить).
8. Выберите **Keys and tokens** (Ключи и маркеры), а затем запишите значения **ключа API клиента** и **секретного ключа API клиента**, которые понадобятся далее.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Настройка Twitter в качестве поставщика удостоверений в клиенте

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
2. Убедитесь, что используете каталог, содержащий клиент Azure AD B2C, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
4. Щелкните **Поставщики удостоверений** и выберите **Добавить**.
5. Укажите **имя**. Например, введите *Twitter*.
6. Щелкните **Тип поставщика удостоверений**, выберите **Twitter** и щелкните **ОК**.
7. Щелкните **Настроить этот поставщик удостоверений** и введите ключ API в поле **идентификатора клиента** и секретный ключ API в поле **секрета клиента**.
8. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Twitter.
