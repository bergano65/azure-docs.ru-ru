---
title: Настройка регистрации и входа с учетной записью GitHub через Azure Active Directory B2C | Документация Майкрософт
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями GitHub, используя Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f3eb5cd62d24ea7251829aed8abba38415835023
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53162823"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью GitHub через Azure Active Directory B2C

> [!NOTE]
> Эта функция предоставляется в предварительной версии.
> 

Чтобы использовать учетную запись GitHub в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, необходимо создать в клиенте приложение, которое будет представлять этого поставщика. Если у вас нет учетной записи GitHub, вы можете получить ее по адресу [https://www.github.com/](https://www.github.com/).

## <a name="create-a-github-oauth-application"></a>Создание приложения OAuth GitHub

1. Войдите на сайт [разработчика GitHub](https://github.com/settings/developers), используя учетные данные GitHub.
2. Выберите **Приложения OAuth**, а затем — **New OAuth App** (Создать приложение OAuth).
3. Заполните поля **Application name** (Имя приложения) и **Homepage URL** (URL-адрес домашней страницы).
4. Введите значение `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` в поле **Authorization callback URL** (URL-адрес обратного вызова авторизации). Замените `your-tenant-name` именем вашего клиента Azure AD B2C. При вводе имени вашего клиента используйте только строчные буквы, даже если в Azure AD B2C имя клиента определено с помощью прописных букв.
5. Щелкните **Register application** (Зарегистрировать приложение).
6. Скопируйте значения **Идентификатор клиента** и **Секрет клиента**. Оба этих значения потребуются при добавлении поставщика удостоверений для вашего клиента.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Настройка учетной записи GitHub в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
2. Убедитесь, что используете каталог, содержащий клиент Azure AD B2C, щелкнув **Фильтр каталога и подписки** в верхнем меню и выбрав каталог, содержащий ваш клиент.
3. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
4. Щелкните **Поставщики удостоверений** и выберите **Добавить**.
5. Укажите **имя**. Например, введите значение *GitHub*.
6. Щелкните **Тип поставщика удостоверений**, выберите **GitHub (Preview)** (GitHub (предварительная версия)) и щелкните **ОК**.
7. Выберите действие **Настроить этот поставщик удостоверений** и введите в поле **Идентификатор клиента** сохраненный идентификатор клиента, а в поле **Секрет клиента** — сохраненный секрет клиента от учетной записи GitHub, которую вы ранее создали.
8. Щелкните **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию учетной записи GitHub.
