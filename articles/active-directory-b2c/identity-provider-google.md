---
title: Настройка регистрации и входа с помощью учетной записи Google
titleSuffix: Azure AD B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Google, используя Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 48955caddb64069f897078f5e47066d9f11d119b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188146"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Google через Azure Active Directory B2C

## <a name="create-a-google-application"></a>Создание приложения Google

Чтобы использовать учетную запись Google в качестве [поставщика удостоверений](authorization-code-flow.md) в Azure Active Directory B2C (Azure AD B2C), необходимо создать приложение в консоли Google Developers. Если у вас еще нет учетной записи Google, вы можете зарегистрироваться [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)по адресу.

1. Выполните вход в [консоль разработчиков Google](https://console.developers.google.com/) с учетными данными для учетной записи Google.
1. В левом верхнем углу страницы выберите список проект, а затем выберите **Новый проект**.
1. Введите **имя проекта**и нажмите кнопку **создать**.
1. Убедитесь, что вы используете новый проект, выбрав раскрывающийся список проект в верхней левой части экрана, выберите проект по имени, а затем щелкните **Открыть**.
1. В меню слева щелкните **экран согласия OAuth** , выберите **внешний**и щелкните **создать**.
Введите **имя** приложения. Введите *b2clogin.com* в разделе " **полномочные домены** " и нажмите кнопку **сохранить**.
1. Выберите **Учетные данные** в меню слева, а затем выберите **Создать учетные данные** > **Идентификатор клиента Oauth**.
1. Из списка **Application type** (Тип приложения) выберите **Web application** (Веб-приложение).
1. В поле **Name** (Имя) введите имя приложения, затем введите `https://your-tenant-name.b2clogin.com` в поле **Authorized JavaScript origins** (Авторизованные источники JavaScript) и `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` — в поле **Authorized redirect URIs** (Авторизованные URI перенаправления). Замените `your-tenant-name` именем вашего клиента. При вводе имени вашего клиента необходимо использовать только строчные буквы, даже если в Azure AD B2C имя клиента определено с прописными буквами.
1. Нажмите кнопку **Создать**.
1. Скопируйте значения **идентификатора клиента** и **секрета клиента**. Оба значения потребуются для настройки Google в качестве поставщика удостоверений в вашем клиенте. **Секрет клиента** — это важные учетные данные безопасности.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Настройка учетной записи Google в качестве поставщика удостоверений

1. Войдите в [портал Azure](https://portal.azure.com/) как глобальный администратор клиента Azure AD B2C.
1. Убедитесь, что используете каталог с клиентом Azure AD B2C, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
1. Выберите **поставщики удостоверений**, а затем выберите **Google**.
1. Введите **имя**. Например, *Google*.
1. В поле **идентификатор клиента**введите идентификатор клиента приложения Google, созданного ранее.
1. В качестве **секрета клиента**введите записанный секрет клиента.
1. Нажмите кнопку **Сохранить**.
