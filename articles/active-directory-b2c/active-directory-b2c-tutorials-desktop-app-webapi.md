---
title: Руководство. Предоставление доступа к веб-API Node.js из классического приложения в Azure Active Directory B2C | Документация Майкрософт
description: Руководство по использованию Active Directory B2C для защиты веб-API Node.js и его вызова из классического приложения NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8c4b2d818549da07faf9ecd28f61b4ed5315f745
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679324"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Руководство. Предоставление доступа к веб-API Node.js из классического приложения с помощью Azure Active Directory B2C

В этом руководстве показано, как вызывать защищенный ресурс веб-API Node.js Azure Active Directory (Azure AD) B2C из классического приложения Windows Presentation Foundation (WPF).

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Добавление приложения веб-API
> * Настройка областей для веб-API.
> * Предоставление разрешения на использование веб-API.
> * Обновление примера для использования приложения.

## <a name="prerequisites"></a>Предварительные требования

Выполните действия и необходимые условия в статье [Руководство. Включение в классическом приложении аутентификации на основе учетных записей с помощью Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Добавление приложения веб-API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Настройка областей

Области предоставляют способ контроля доступа к защищенным ресурсам. Области используются веб-API для реализации управления доступом на уровне области. Например, некоторые пользователи могут иметь доступ на чтение и запись, тогда как другие пользователи могут иметь разрешения только на чтение. В этом руководстве вы определяете разрешения на чтение для веб-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Предоставление разрешений

Чтобы вызвать защищенный веб-API из приложения, необходимо предоставить приложению разрешения на доступ к API. Как часть предварительного требования вы создали веб-приложение *app1* в Azure AD B2C. Используйте это приложение для вызова веб-API.

1. Щелкните **Приложения**, а затем выберите *nativeapp1*.
1. Щелкните **Доступ через API**, а затем выберите **Добавить**.
1. В раскрывающемся списке **Выбрать API** выберите *webapi1*.
1. В раскрывающемся списке **Выбрать области** выберите области, определенные ранее. Например, *demo.read* и *demo.write*.
1. Нажмите кнопку **ОК**.

Пользователь выполняет проверку подлинности с помощью Azure AD B2C для использования классического приложения WPF. Классическое приложение получает предоставление авторизации из Azure AD B2C для доступа к защищенному веб-API.

## <a name="configure-the-sample"></a>Настройка примера

Теперь, когда веб-API зарегистрирован и определены области, необходимо настроить код веб-API для использования клиента Azure AD B2C. С помощью этого руководства вы настроите пример веб-приложения Node.js, который можно скачать из репозитория GitHub.

[Загрузите ZIP-файл](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) или клонируйте пример приложения с GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Пример веб-API Node.js с помощью библиотеки Passport.js активирует Azure AD B2C для защиты вызовов API.

1. Откройте файл `index.js` .
2. Настройте пример, указав сведения о регистрации клиента Azure AD B2C. Измените следующие строки кода:

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Запуск примера

1. Откройте командную строку Node.js.
2. Перейдите в каталог, содержащий пример приложения Node.js. Например `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Выполните следующие команды:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Запуск классического приложения

1. Откройте решение **active-directory-b2c-wpf** в Visual Studio.
2. Нажмите клавишу **F5**, чтобы запустить классическое приложение.
3. Войдите с помощью адреса электронной почты и пароля, которые использовались при работе с [руководством по проверке подлинности пользователей с помощью Azure Active Directory B2C в классическом приложении](active-directory-b2c-tutorials-desktop-app.md).
4. Нажмите кнопку **вызова API**.

Классическое приложение отправляет запрос к веб-API и получает ответ, содержащий отображаемое имя пользователя, вошедшего в систему. Защищенное классическое приложение вызывает защищенный веб-API в клиенте Azure AD B2C.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Добавление приложения веб-API
> * Настройка областей для веб-API.
> * Предоставление разрешения на использование веб-API.
> * Обновление примера для использования приложения.

> [!div class="nextstepaction"]
> [Руководство. Добавление поставщиков удостоверений приложениям в Azure Active Directory B2C](tutorial-add-identity-providers.md)
