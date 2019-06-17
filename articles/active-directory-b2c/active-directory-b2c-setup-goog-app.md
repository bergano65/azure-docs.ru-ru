---
title: Настройка регистрации и входа с учетной записью Google — Azure Active Directory B2C | Документация Майкрософт
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Google, используя Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f7e092e27f73901810a9f7edd210e3513c54095e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508515"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Google через Azure Active Directory B2C

## <a name="create-a-google-application"></a>Создание приложения Google

Чтобы использовать учетную запись Google в качестве [поставщика удостоверений](active-directory-b2c-reference-oauth-code.md) для Azure Active Directory (Azure AD) B2C, необходимо создать в клиенте приложение, которое будет представлять этого поставщика. Если у вас нет учетной записи Google, вы можете получить ее по адресу [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Выполните вход в [консоль разработчиков Google](https://console.developers.google.com/) с учетными данными для учетной записи Google.
2. В левом верхнем углу страницы выберите список проектов и выберите **новый проект**.
3. Введите значение в поле **Имя проекта**, нажмите кнопку **Создать**, а затем убедитесь, что вы используете новый проект.
4. Выберите **Учетные данные** в меню слева, а затем выберите **Создать учетные данные** > **Идентификатор клиента Oauth**.
5. Из списка **Application type** (Тип приложения) выберите **Web application** (Веб-приложение).
6. В поле **Name** (Имя) введите имя приложения, затем введите `https://your-tenant-name.b2clogin.com` в поле **Authorized JavaScript origins** (Авторизованные источники JavaScript) и `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` — в поле **Authorized redirect URIs** (Авторизованные URI перенаправления). Замените `your-tenant-name` именем вашего клиента. При вводе имени вашего клиента необходимо использовать только строчные буквы, даже если в Azure AD B2C имя клиента определено с прописными буквами.
7. Нажмите кнопку **Создать**.
8. Скопируйте значения **Идентификатор клиента** и **Секрет клиента**. Оба значения потребуются для настройки Google в качестве поставщика удостоверений в вашем клиенте. **Секрет клиента** — это важные учетные данные безопасности.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Настройка учетной записи Google в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
2. Убедитесь, что используете каталог, содержащий клиент Azure AD B2C, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
4. Щелкните **Поставщики удостоверений** и выберите **Добавить**.
5. Введите **Имя**. Например, введите *Google*.
6. Щелкните **Тип поставщика удостоверений**, выберите **Google** и щелкните **ОК**.
7. Выберите действие **Настроить этот поставщик удостоверений** и введите в поле **Идентификатор клиента** сохраненный идентификатор клиента, а в поле **Секрет клиента** — сохраненный секрет клиента от учетной записи Google, которую вы ранее создали.
8. Щелкните **ОК**, а затем выберите **Создать**, чтобы сохранить конфигурацию Google.

