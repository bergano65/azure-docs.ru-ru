---
title: Настройка регистрации и входа с учетной записью Weibo через Azure Active Directory B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Weibo, используя Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 428149fbc015037fa8c92bad6fe72cbd97aad5d7
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622243"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Weibo через Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Создание приложения Weibo

Чтобы использовать учетную запись Weibo в качестве поставщика удостоверений для Azure Active Directory (Azure AD) B2C, необходимо создать в клиенте приложение, которое будет представлять этого поставщика. Если у вас еще нет учетной записи Weibo, вы можете зарегистрироваться по [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us)адресу.

1. Выполните вход на [портал разработчиков Weibo](https://open.weibo.com/) с учетными данными от учетной записи Weibo.
1. После входа щелкните отображаемое имя в правом верхнем углу.
1. Из раскрывающегося списка выберите **编辑开发者信息** (Изменить информацию о разработчике).
1. Введите необходимые сведения и щелкните **提交** (Передать).
1. Выполните проверку по электронной почте.
1. Перейдите на [страницу проверки удостоверения](https://open.weibo.com/developers/identity/edit).
1. Введите необходимые сведения и щелкните **提交** (Передать).

### <a name="register-a-weibo-application"></a>Регистрация приложения Weibo

1. Перейдите на [новую страницу регистрации приложения Weibo](https://open.weibo.com/apps/new).
1. Введите необходимые сведения о приложении.
1. Выберите **创建** (Создать).
1. Скопируйте значения **ключа приложения** и **секрета приложения**. Оба этих значения потребуются при добавлении поставщика удостоверений для вашего клиента.
1. Передайте необходимые фотографии и введите требуемые сведения.
1. Выберите **保存以上信息** (Сохранить).
1. Выберите **高级信息** (Дополнительные сведения).
1. Выберите **编辑** (Изменить) рядом с полем **授权设置** (URL-адрес перенаправления) для OAuth2.0.
1. Введите `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` в поле **授权设置** (URL-адрес перенаправления) для OAuth2.0. Например, если имя клиента — contoso, задайте URL-адрес `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Выберите **提交** (Отправить).

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Настройка учетной записи Weibo в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
1. Убедитесь, что вы используете каталог, содержащий клиент Azure AD B2C, выбрав фильтр " **каталог и подписка** " в верхнем меню и выбрав Каталог, содержащий ваш клиент.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
1. Выберите **поставщики удостоверений**, а затем выберите **Weibo (Предварительная версия)** .
1. Введите **Имя**. Например, *Weibo*.
1. В поле **идентификатор клиента**введите ключ приложения приложения Weibo, созданного ранее.
1. В качестве **секрета клиента**введите записанный секрет приложения.
1. Щелкните **Сохранить**.
