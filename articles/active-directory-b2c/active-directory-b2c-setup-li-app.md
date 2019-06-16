---
title: Настройка регистрации и входа с учетной записью LinkedIn — Azure Active Directory B2C | Документация Майкрософт
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями LinkedIn, используя Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5ffe75a58a8cfb2409af3a65ff822023d7ccf5d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508471"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью LinkedIn через Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Создание приложения LinkedIn

Чтобы использовать учетную запись LinkedIn в качестве [поставщика удостоверений](active-directory-b2c-reference-oauth-code.md) для Azure Active Directory (Azure AD) B2C, необходимо создать в клиенте приложение, которое будет представлять этого поставщика. Если у вас нет учетной записи LinkedIn, вы можете получить ее по адресу [https://www.linkedin.com/](https://www.linkedin.com/).

1. Выполните вход на [сайт разработчиков LinkedIn](https://www.developer.linkedin.com/) с учетными данными для учетной записи LinkedIn.
2. Выберите **My Apps** (Мои приложения) и щелкните **Create Application** (Создать приложение).
3. Заполните поля **Company Name** (Имя компании), **Application Name** (Имя приложения), **Application Description** (Описание приложения), **Application Logo** (Логотип приложения), **Application Use** (Использование приложения), **Website URL** (URL-адрес веб-сайта), **Business Email** (Рабочий адрес электронной почты) и **Business Phone** (Рабочий телефон).
4. Примите **условия использования LinkedIn API** и нажмите кнопку **Отправить**.
5. Скопируйте значения **Идентификатор клиента** и **Секрет клиента**. Их можно найти в разделе **Ключи аутентификации**. Оба значения необходимы для настройки LinkedIn в качестве поставщика удостоверений для вашего клиента. **Секрет клиента** — это важные учетные данные безопасности.
6. Введите `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` в поле **Authorized Redirect URLs** (Авторизованные URL-адреса перенаправления). Замените `your-tenant-name` именем вашего клиента. При вводе имени вашего клиента необходимо использовать только строчные буквы, даже если в Azure AD B2C имя клиента определено с прописными буквами. Щелкните **Добавить**, а затем выберите **Обновить**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Настройка учетной записи LinkedIn в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
2. Убедитесь, что используете каталог, содержащий клиент Azure AD B2C, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
4. Щелкните **Поставщики удостоверений** и выберите **Добавить**.
5. Укажите **имя**. Например, введите *LinkedIn*.
6. Щелкните **Тип поставщика удостоверений**, выберите **LinkedIn** и щелкните **ОК**.
7. Выберите действие **Настроить этот поставщик удостоверений** и введите в поле **Идентификатор клиента** сохраненный идентификатор клиента, а в поле **Секрет клиента** — сохраненный секрет клиента от учетной записи LinkedIn, которую вы ранее создали.
8. Щелкните **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию учетной записи LinkedIn.

## <a name="migration-from-v10-to-v20"></a>Миграция с версии 1.0, версии 2.0

Недавно LinkedIn [обновить свои API из версии 1.0 для версии 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). В рамках миграции Azure AD B2C только имеет возможность получить полное имя пользователя, LinkedIn, во время регистрации. Если адрес электронной почты является одним из атрибутов, которые собираются во время регистрации, пользователь должен вручную введите адрес электронной почты и его проверки.
