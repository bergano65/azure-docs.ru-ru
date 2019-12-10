---
title: Настройка регистрации и входа с помощью учетной записи Twitter
titleSuffix: Azure AD B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Twitter, используя Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8b316fdb185c8a9c78e1a0c9be5f7d57b809141c
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950448"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Twitter через Azure Active Directory B2C

## <a name="create-an-application"></a>Создание приложения

Чтобы использовать Twitter в качестве поставщика удостоверений в Azure AD B2C, необходимо создать приложение Twitter. Если у вас еще нет учетной записи Twitter, вы можете зарегистрироваться по адресу [https://twitter.com/signup](https://twitter.com/signup).

1. Выполните вход на [веб-сайт разработчиков Twitter](https://developer.twitter.com/en/apps) с учетными данными для учетной записи Twitter.
1. Выберите **Create an app** (Создать приложение).
1. Введите **имя приложения** и **описание приложения**.
1. В поле **Website URL** (URL-адрес веб-сайта) введите `https://your-tenant.b2clogin.com`. Замените `your-tenant` именем вашего клиента. Пример: https://contosob2c.b2clogin.com.
1. В поле **Callback URL** (URL-адрес обратного вызова) введите значение `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Замените `your-tenant` именем своего клиента, а `your-user-flow-Id` — идентификатором своего потока пользователя. Пример: `b2c_1A_signup_signin_twitter`. При вводе имени клиента и идентификатора потока пользователя необходимо использовать все строчные буквы, даже если они определены с прописными буквами в Azure AD B2C.
1. В нижней части страницы прочтите условия использования и примите их, а затем щелкните **Create** (Создать).
1. На странице **сведений о приложении** выберите **Edit > Edit details** (Изменить > Изменить сведения), установите флажок в поле **Enable Sign in with Twitter** (Включить вход через Twitter), а затем нажмите кнопку **Save** (Сохранить).
1. Выберите **Keys and tokens** (Ключи и маркеры), а затем запишите значения **ключа API клиента** и **секретного ключа API клиента**, которые понадобятся далее.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Настройка Twitter в качестве поставщика удостоверений в клиенте

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
1. Убедитесь, что используете каталог с клиентом Azure AD B2C, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
1. Выберите **поставщики удостоверений**, а затем выберите **Twitter**.
1. Введите **Имя**. Например, *Twitter*.
1. В поле **идентификатор клиента**введите ключ API потребителя созданного ранее приложения Twitter.
1. В качестве **секрета клиента**введите записанный секретный ключ API-интерфейса потребителя.
1. Щелкните **Сохранить**.
