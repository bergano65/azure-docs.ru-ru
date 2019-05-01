---
title: Настройка регистрации и входа с учетной записью Microsoft — Azure Active Directory B2C | Документация Майкрософт
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Microsoft, используя Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d6557d8dccd3c61307bc3d29c86d98722d793170
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703776"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Microsoft через Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Создание приложения для учетной записи Майкрософт

Чтобы использовать учетную запись Microsoft в качестве [поставщика удостоверений](active-directory-b2c-reference-oidc.md) для Azure Active Directory (Azure AD) B2C, необходимо создать в клиенте приложение, которое будет представлять этого поставщика. Если у вас нет учетной записи Microsoft, вы можете получить ее по адресу [https://www.live.com/](https://www.live.com/).

1. Войдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) и войдите учетные данные от учетной записи Майкрософт.
2. В правом верхнем углу выберите **Добавить приложение**.
3. Введите значение **Name** (Имя) для приложения. Например, *MSAapp1*.
4. Выберите **Создать новый пароль** и скопируйте пароль. Он понадобится вам при настройке поставщика удостоверений. Скопируйте также **идентификатор приложения**. 
5. Щелкните **Add platform** (Добавить платформу) и выберите вариант **Веб**.
4. Введите значение `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` в поле **Redirect URLs** (URI перенаправления). Замените `your-tenant-name` именем вашего клиента.
5. Щелкните **Сохранить**.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Настройка учетной записи Microsoft в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
2. Убедитесь, что используете каталог, содержащий клиент Azure AD B2C, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
4. Щелкните **Поставщики удостоверений** и выберите **Добавить**.
5. Укажите **Имя**. Например, введите *MSA*.
6. Щелкните **Тип поставщика удостоверений**, выберите **Учетная запись Майкрософт** и щелкните **ОК**.
7. Выберите действие **Настроить этот поставщик удостоверений** и введите в поле **Идентификатор клиента** сохраненный идентификатор приложения, а в поле **Секрет клиента** — сохраненный пароль от приложения учетной записи Майкрософт, которое вы ранее создали.
8. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию учетной записи Майкрософт.

