---
title: Настройка регистрации и входа с помощью учетной записи LinkedIn. Azure Active Directory B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями LinkedIn, используя Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ddc42bc9315d50b8b8cdd40264de561d0edf5c7d
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622387"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью LinkedIn через Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Создание приложения LinkedIn

Чтобы использовать учетную запись LinkedIn в качестве [поставщика удостоверений](active-directory-b2c-reference-oauth-code.md) для Azure Active Directory (Azure AD) B2C, необходимо создать в клиенте приложение, которое будет представлять этого поставщика. Если у вас еще нет учетной записи LinkedIn, вы можете зарегистрироваться по [https://www.linkedin.com/](https://www.linkedin.com/)адресу.

1. Выполните вход на [сайт разработчиков LinkedIn](https://www.developer.linkedin.com/) с учетными данными для учетной записи LinkedIn.
1. Выберите **My Apps** (Мои приложения) и щелкните **Create Application** (Создать приложение).
1. Заполните поля **Company Name** (Имя компании), **Application Name** (Имя приложения), **Application Description** (Описание приложения), **Application Logo** (Логотип приложения), **Application Use** (Использование приложения), **Website URL** (URL-адрес веб-сайта), **Business Email** (Рабочий адрес электронной почты) и **Business Phone** (Рабочий телефон).
1. Примите **условия использования LinkedIn API** и нажмите кнопку **Отправить**.
1. Скопируйте значения **Идентификатор клиента** и **Секрет клиента**. Их можно найти в разделе **Ключи аутентификации**. Оба значения необходимы для настройки LinkedIn в качестве поставщика удостоверений для вашего клиента. **Секрет клиента** — это важные учетные данные безопасности.
1. Введите `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` в поле **Authorized Redirect URLs** (Авторизованные URL-адреса перенаправления). Замените `your-tenant-name` именем вашего клиента. При вводе имени вашего клиента необходимо использовать только строчные буквы, даже если в Azure AD B2C имя клиента определено с прописными буквами. Щелкните **Добавить**, а затем выберите **Обновить**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Настройка учетной записи LinkedIn в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C, выбрав фильтр " **каталог и подписка** " в верхнем меню и выбрав Каталог, содержащий ваш клиент.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
1. Выберите **поставщики удостоверений**, а затем выберите **LinkedIn**.
1. Введите **Имя**. Например, *LinkedIn*.
1. В поле **идентификатор клиента**введите идентификатор клиента приложения LinkedIn, созданного ранее.
1. В качестве **секрета клиента**введите записанный секрет клиента.
1. Щелкните **Сохранить**.

## <a name="migration-from-v10-to-v20"></a>Миграция с версии 1.0 на v 2.0

Мы недавно [обновили API-интерфейсы с версии 1.0 до версии 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). В ходе миграции Azure AD B2C удается получить полное имя пользователя LinkedIn во время регистрации... Если адрес электронной почты является одним из атрибутов, собираемых во время регистрации, пользователь должен вручную ввести адрес электронной почты и проверить его.
