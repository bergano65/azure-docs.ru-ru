---
title: Настройка регистрации и входа с помощью учетной записи WeChat
titleSuffix: Azure AD B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями WeChat при помощи Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7cbc41247d7b99eb63730ae4326808e64c663e8f
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950533"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью WeChat в Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>Создание приложения WeChat

Чтобы использовать учетную запись WeChat в качестве поставщика удостоверений в Azure Active Directory B2C (Azure AD B2C), необходимо создать в своем клиенте приложение, которое его представляет. Если у вас еще нет учетной записи WeChat, то сведения можно получить по адресу [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Регистрация приложения WeChat

1. Войдите в [https://open.weixin.qq.com/](https://open.weixin.qq.com/) с учетными данными WeChat.
1. Выберите **管理中心** (Центр управления).
1. Выполните отображаемые инструкции, чтобы зарегистрировать новое приложение.
1. Введите значение `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` в поле **授权回调域** (URL-адрес обратного вызова). Например, если имя клиента — contoso, задайте URL-адрес `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Скопируйте **идентификатор приложения** и **ключ приложения**. Эти значения потребуются при добавлении поставщика удостоверений для вашего клиента.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Настройка WeChat в качестве поставщика удостоверений в клиенте

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
1. Убедитесь, что используете каталог с клиентом Azure AD B2C, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
1. Выберите **поставщики удостоверений**, а затем выберите **WeChat (Предварительная версия)** .
1. Введите **Имя**. Например, *WeChat*.
1. В поле **идентификатор клиента**введите идентификатор приложения WeChat, созданного ранее.
1. В качестве **секрета клиента**введите ЗАПИСАННЫЙ ключ приложения.
1. Щелкните **Сохранить**.
