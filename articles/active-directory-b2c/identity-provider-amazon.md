---
title: Настройка регистрации и входа с помощью учетной записи Amazon
titleSuffix: Azure AD B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Amazon, используя Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d1a771cb13fcfa76449500ad71c67dcf7c446fa4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388448"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Amazon через Azure Active Directory B2C

## <a name="create-an-app-in-the-amazon-developer-console"></a>Создание приложения в консоли разработчика Amazon

Чтобы использовать учетную запись Amazon в качестве федеративного поставщика удостоверений в Azure Active Directory B2C (Azure AD B2C), необходимо создать приложение в [службах и технологиях Amazon Developer](https://developer.amazon.com). Если у вас еще нет учетной записи Amazon, вы можете зарегистрироваться по адресу [https://www.amazon.com/](https://www.amazon.com/) .

> [!NOTE]  
> Используйте следующие URL-адреса на **шаге 8** ниже, заменив `your-tenant-name` именем своего клиента. При вводе имени клиента используйте все строчные буквы, даже если клиент определен с прописными буквами в Azure AD B2C.
> - Для **разрешенных источников**введите`https://your-tenant-name.b2clogin.com` 
> - Для **разрешенных URL-адресов возврата**введите`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Настройка учетной записи Amazon в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
1. Убедитесь, что используете каталог с клиентом Azure AD B2C, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
1. Выберите **поставщики удостоверений**, а затем щелкните **Amazon**.
1. Введите **Имя**. Например, *Amazon*.
1. В поле **идентификатор клиента**введите идентификатор клиента приложения Amazon, созданного ранее.
1. В качестве **секрета клиента**введите записанный секрет клиента.
1. Щелкните **Сохранить**.
