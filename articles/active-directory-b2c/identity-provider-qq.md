---
title: Настройка регистрации и входа с учетной записью QQ через Azure Active Directory B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями QQ, используя Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d230bc8a1e9bf388e1cca4e3a3a691223146d734
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387989"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью QQ через Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Создание приложения QQ

Чтобы использовать учетную запись QQ в качестве поставщика удостоверений в Azure Active Directory B2C (Azure AD B2C), необходимо создать в своем клиенте приложение, которое его представляет. Если у вас еще нет учетной записи QQ, вы можете зарегистрироваться по адресу [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) .

### <a name="register-for-the-qq-developer-program"></a>Регистрация в программе разработчиков QQ

1. Войдите на [портал разработчиков QQ](http://open.qq.com) с учетными данными от учетной записи QQ.
1. После входа перейдите на страницу, [https://open.qq.com/reg](https://open.qq.com/reg) чтобы зарегистрироваться в качестве разработчика.
1. Выберите **个人**(Индивидуальный разработчик).
1. Введите необходимые сведения и щелкните **下一步** (Следующий шаг).
1. Выполните проверку по электронной почте. После регистрации в качестве разработчика вам придется подождать утверждения в течение нескольких дней.

### <a name="register-a-qq-application"></a>Регистрация приложения QQ

1. Перейдите по адресу [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
1. Выберите **应用管理**(Управление приложениями).
1. Выберите **创建应用**(Создать приложение) и введите необходимые сведения.
1. Введите значение `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` в поле **授权回调域** (URL-адрес обратного вызова). Например, если `tenant_name` — contoso, задайте URL-адрес `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Выберите **创建应用** (Создать приложение).
1. На странице подтверждения выберите **应用管理** (Управление приложениями), чтобы вернуться на страницу управления приложениями.
1. Выберите **查看** (Просмотр) рядом с только что созданным приложением.
1. Выберите **修改** (Редактирование).
1. Скопируйте **идентификатор приложения** и **ключ приложения**. Оба этих значения потребуются при добавлении поставщика удостоверений для вашего клиента.

## <a name="configure-qq-as-an-identity-provider"></a>Настройка QQ в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Выберите значок **Каталог и подписка** в верхней панели инструментов портала, а затем выберите каталог, содержащий клиент Azure AD B2C.
1. На портале Azure найдите и выберите **Azure AD B2C**.
1. Выберите **поставщики удостоверений**, а затем выберите **QQ (Предварительная версия)**.
1. Введите **Имя**. Например, *QQ*.
1. В поле **идентификатор клиента**введите идентификатор приложения QQ, созданного ранее.
1. В качестве **секрета клиента**введите ЗАПИСАННЫЙ ключ приложения.
1. Щелкните **Сохранить**.
