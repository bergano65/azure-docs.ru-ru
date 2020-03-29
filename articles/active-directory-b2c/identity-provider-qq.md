---
title: Настройка регистрации и входа с учетной записью QQ через Azure Active Directory B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями QQ, используя Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b0f15124c64e5cca54112987d486ddadaca79452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187993"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью QQ через Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Создание приложения QQ

Чтобы использовать учетную запись в качестве поставщика идентификационных данных в Azure Active Directory B2C (Azure AD B2C), необходимо создать приложение в интересующий его клиента. Если у вас еще нет учетной записи, [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033)вы можете зарегистрироваться в .

### <a name="register-for-the-qq-developer-program"></a>Регистрация в программе разработчиков QQ

1. Войдите на [портал разработчиков QQ](http://open.qq.com) с учетными данными от учетной записи QQ.
1. После вху-на-е, перейдите, чтобы [https://open.qq.com/reg](https://open.qq.com/reg) зарегистрироваться в качестве разработчика.
1. Выберите **个人**(Индивидуальный разработчик).
1. Введите необходимые сведения и щелкните **下一步** (Следующий шаг).
1. Выполните проверку по электронной почте. После регистрации в качестве разработчика вам придется подождать утверждения в течение нескольких дней.

### <a name="register-a-qq-application"></a>Регистрация приложения QQ

1. Перейти [https://connect.qq.com/index.html](https://connect.qq.com/index.html)к .
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
1. Выберите **значок каталога и подписки** в панели инструментов портала, а затем выберите каталог, содержащий ваш клиент Azure AD B2C.
1. На портале Azure ищите **Azure AD B2C.**
1. Выберите **поставщиков идентификационных данных,** а затем выберите **кью (Предварительный просмотр).**
1. Введите **имя**. Например, *кью*.
1. Для **идентификатора клиента**введите идентификатор APP приложения, созданного ранее.
1. Для **секрета клиента,** введите APP KEY, который вы записали.
1. Нажмите кнопку **Сохранить**.
