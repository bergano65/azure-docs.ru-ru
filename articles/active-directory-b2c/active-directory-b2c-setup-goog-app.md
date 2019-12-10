---
title: Настройка регистрации и входа с помощью учетной записи Google
titleSuffix: Azure AD B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Google, используя Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1e23c79b1e09f3e3a7aaa21b9257bfe6bd43f7e8
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950479"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Google через Azure Active Directory B2C

## <a name="create-a-google-application"></a>Создание приложения Google

Чтобы использовать учетную запись Google в качестве [поставщика удостоверений](active-directory-b2c-reference-oauth-code.md) в Azure Active Directory B2C (Azure AD B2C), необходимо создать в своем клиенте приложение, которое его представляет. Если у вас еще нет учетной записи Google, вы можете зарегистрироваться по адресу [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Выполните вход в [консоль разработчиков Google](https://console.developers.google.com/) с учетными данными для учетной записи Google.
1. В левом верхнем углу страницы выберите список проект, а затем выберите **Новый проект**.
1. Введите значение в поле **Имя проекта**, нажмите кнопку **Создать**, а затем убедитесь, что вы используете новый проект.
1. Выберите **Учетные данные** в меню слева, а затем выберите **Создать учетные данные** > **Идентификатор клиента Oauth**.
1. Из списка **Application type** (Тип приложения) выберите **Web application** (Веб-приложение).
1. В поле **Name** (Имя) введите имя приложения, затем введите `https://your-tenant-name.b2clogin.com` в поле **Authorized JavaScript origins** (Авторизованные источники JavaScript) и `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` — в поле **Authorized redirect URIs** (Авторизованные URI перенаправления). Замените `your-tenant-name` именем вашего клиента. При вводе имени вашего клиента необходимо использовать только строчные буквы, даже если в Azure AD B2C имя клиента определено с прописными буквами.
1. Щелкните **Create**(Создать).
1. Скопируйте значения **Идентификатор клиента** и **Секрет клиента**. Оба значения потребуются для настройки Google в качестве поставщика удостоверений в вашем клиенте. **Секрет клиента** — это важные учетные данные безопасности.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Настройка учетной записи Google в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
1. Убедитесь, что используете каталог с клиентом Azure AD B2C, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
1. Выберите **поставщики удостоверений**, а затем выберите **Google**.
1. Введите **Имя**. Например, *Google*.
1. В поле **идентификатор клиента**введите идентификатор клиента приложения Google, созданного ранее.
1. В качестве **секрета клиента**введите записанный секрет клиента.
1. Щелкните **Сохранить**.
