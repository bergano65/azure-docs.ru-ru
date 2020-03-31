---
title: Настройка регистрации и входиный займ с учетной записью Amazon
titleSuffix: Azure AD B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Amazon, используя Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d4538c1d15aeae624f5d73e9985448bda2fd8f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188466"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Amazon через Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Создание приложения Amazon

Чтобы использовать учетную запись Amazon в качестве [поставщика идентификационных данных](authorization-code-flow.md) в Azure Active Directory B2C (Azure AD B2C), необходимо создать приложение в интересующий его клиента. Если у вас еще нет учетной записи [https://www.amazon.com/](https://www.amazon.com/)Amazon, вы можете зарегистрироваться в .

1. Выполните вход в [Amazon Developer Center](https://login.amazon.com/) с учетными данными от учетной записи Amazon.
1. Если это еще не сделано, нажмите кнопку **Sign Up**(Регистрация), выполните действия для регистрации разработчика и примите условия политики.
1. Щелкните **Register new application** (Зарегистрировать новое приложение).
1. Введите значения **Имя**, **Описание**, и **URL-адрес заявления о конфиденциальности**, а затем щелкните **Сохранить**. Уведомление о конфиденциальности — это управляемая вами страница, где предоставляются сведения о конфиденциальности для пользователей.
1. В разделе **Web Settings** (Веб-параметры) скопируйте значение **Client ID** (Идентификатор клиента). Выберите **Показать секрет**, чтобы просмотреть и скопировать секрет клиента. Оба значения потребуются для настройки учетной записи Amazon в качестве поставщика удостоверений в вашем клиенте. **Секрет клиента**— важный элемент обеспечения безопасности.
1. В разделе **Web Settings** (Веб-параметры) выберите действие **Редактировать**, а затем введите значение `https://your-tenant-name.b2clogin.com` в поле **Allowed JavaScript Origins** (Допустимые источники JavaScript) и значение `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` в поле **Allowed Return URLs** (Допустимые URL-адреса возврата). Замените `your-tenant-name` именем вашего клиента. При вводе имени вашего клиента необходимо использовать только строчные буквы, даже если в Azure AD B2C имя клиента определено с прописными буквами.
1. Нажмите **Сохранить**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Настройка учетной записи Amazon в качестве поставщика удостоверений

1. Вопийте на [портале Azure](https://portal.azure.com/) в качестве глобального администратора вашего арендатора Azure AD B2C.
1. Убедитесь, что используете каталог с клиентом Azure AD B2C, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
1. Выберите **поставщиков идентификационных данных,** затем выберите **Amazon.**
1. Введите **имя**. Например, *Amazon*.
1. Для **идентификатора клиента**введите идентификатор клиента приложения Amazon, созданного ранее.
1. Для **секрета клиента,** введите секрет клиента, который вы записали.
1. Нажмите кнопку **Сохранить**.
