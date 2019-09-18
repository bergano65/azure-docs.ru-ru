---
title: Настройка регистрации и входа с помощью учетной записи Amazon — Azure Active Directory B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Amazon, используя Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bddbd9b588d8ffc2ace3a937fa2c72876eb5e4e9
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065215"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Amazon через Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Создание приложения Amazon

Чтобы использовать учетную запись Amazon в качестве [поставщика удостоверений](active-directory-b2c-reference-oauth-code.md) в Azure Active Directory B2C (Azure AD B2C), необходимо создать в своем клиенте приложение, которое его представляет. Если у вас еще нет учетной записи Amazon, вы можете зарегистрироваться [https://www.amazon.com/](https://www.amazon.com/)по адресу.

1. Выполните вход в [Amazon Developer Center](https://login.amazon.com/) с учетными данными от учетной записи Amazon.
1. Если это еще не сделано, нажмите кнопку **Sign Up**(Регистрация), выполните действия для регистрации разработчика и примите условия политики.
1. Щелкните **Register new application** (Зарегистрировать новое приложение).
1. Введите значения **Имя**, **Описание**, и **URL-адрес заявления о конфиденциальности**, а затем щелкните **Сохранить**. Уведомление о конфиденциальности — это управляемая вами страница, где предоставляются сведения о конфиденциальности для пользователей.
1. В разделе **Web Settings** (Веб-параметры) скопируйте значение **Client ID** (Идентификатор клиента). Выберите **Показать секрет**, чтобы просмотреть и скопировать секрет клиента. Оба значения потребуются для настройки учетной записи Amazon в качестве поставщика удостоверений в вашем клиенте. **Секрет клиента** — это важные учетные данные безопасности.
1. В разделе **Web Settings** (Веб-параметры) выберите действие **Редактировать**, а затем введите значение `https://your-tenant-name.b2clogin.com` в поле **Allowed JavaScript Origins** (Допустимые источники JavaScript) и значение `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` в поле **Allowed Return URLs** (Допустимые URL-адреса возврата). Замените `your-tenant-name` именем вашего клиента. При вводе имени вашего клиента необходимо использовать только строчные буквы, даже если в Azure AD B2C имя клиента определено с прописными буквами.
1. Нажмите кнопку **Сохранить**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Настройка учетной записи Amazon в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C, выбрав фильтр " **каталог и подписка** " в верхнем меню и выбрав Каталог, содержащий ваш клиент.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
1. Выберите **поставщики удостоверений**, а затем щелкните **Amazon**.
1. Введите **Имя**. Например, *Amazon*.
1. В поле **идентификатор клиента**введите идентификатор клиента приложения Amazon, созданного ранее.
1. В качестве **секрета клиента**введите записанный секрет клиента.
1. Щелкните **Сохранить**.
