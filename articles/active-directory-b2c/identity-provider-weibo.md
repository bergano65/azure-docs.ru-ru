---
title: Настройка регистрации и входиный займ с учетной записью Weibo
titleSuffix: Azure AD B2C
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Weibo, используя Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e99b5717cdcc32d8fd138d1edf5fe1bd3283c70e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187905"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Weibo через Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Создание приложения Weibo

Чтобы использовать учетную запись Weibo в качестве поставщика идентификационных данных в Azure Active Directory B2C (Azure AD B2C), необходимо создать приложение в интересующий его клиента. Если у вас еще нет учетной записи Weibo, вы можете зарегистрироваться в [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us).

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

1. Вопийте на [портале Azure](https://portal.azure.com/) в качестве глобального администратора вашего арендатора Azure AD B2C.
1. Убедитесь, что используете каталог с клиентом Azure AD B2C, выбрав фильтр **Каталог и подписка** в меню вверху и каталог с вашим клиентом.
1. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
1. Выберите **поставщиков идентификационных данных,** затем выберите **Weibo (Предварительный просмотр).**
1. Введите **имя**. Например, *Weibo*.
1. Для **идентификатора клиента**введите клавишу приложения App ibo, созданное ранее.
1. Для **секрета клиента,** введите Секрет app который вы записали.
1. Нажмите кнопку **Сохранить**.
