---
title: Руководство. Включение проверки подлинности в собственном клиентском приложении c помощью Azure Active Directory B2C | Документация Майкрософт
description: Руководство по предоставлению пользователю данных для входа в классическое приложение .NET с помощью Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3740a032db6ca9fd0fb88ce348610684d9f895bc
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326332"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>Руководство по Включение проверки подлинности в собственном клиентском приложении с помощью Azure Active Directory B2C

В этом руководстве показано, как использовать Azure Active Directory (Azure AD) B2C для входа и регистрации пользователей в классическом приложении Windows Presentation Foundation (WPF). Azure AD B2C позволяет приложениям выполнять проверку подлинности учетных записей социальных сетей, корпоративных учетных записей и учетных записей Azure Active Directory с помощью стандартных протоколов.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * добавление собственного клиентского приложения;
> * настройка примера для использования приложения;
> * регистрация с помощью потока пользователя.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

- [Создайте потоки пользователя](tutorial-create-user-flows.md), чтобы обеспечить взаимодействие с пользователями в приложении.
- Установите [Visual Studio 2019](https://www.visualstudio.com/downloads/) с рабочими нагрузками **Разработка классических приложений .NET** и **ASP.NET и веб-разработка**.

## <a name="add-the-native-client-application"></a>добавление собственного клиентского приложения;

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Запишите **идентификатор приложения**. Он вам потребуется в дальнейшем.

## <a name="configure-the-sample"></a>Настройка примера

С помощью этого руководства вы настроите пример, который можно скачать из репозитория GitHub. Пример классического приложения WPF демонстрирует регистрацию, вход в систему и вызывает защищенный веб-API в Azure AD B2C. [Скачайте ZIP-файл](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [просмотрите репозиторий](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) или клонируйте пример с GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Чтобы изменить параметры приложения, замените `<your-tenant-name>` именем клиента и `<application-ID` записанным идентификатором приложения.

1. Откройте решение `active-directory-b2c-wpf` в Visual Studio.
2. В проекте `active-directory-b2c-wpf` откройте файл **App.xaml.cs** и внесите следующие изменения:

    ```csharp
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. Обновите переменную **PolicySignUpSignIn** с помощью записанного имени потока пользователя.

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Запуск примера

Нажмите клавишу **F5**, чтобы выполнить сборку и запустить пример.

### <a name="sign-up-using-an-email-address"></a>Регистрация с помощью адреса электронной почты

1. Выберите **Войти**, чтобы зарегистрироваться как пользователь. Используется поток пользователя **B2C_1_signupsignin1**.
2. Появляется страница входа в Azure AD B2C со ссылкой для регистрации. Щелкните ссылку **Зарегистрироваться сейчас**, так как у вас нет учетной записи.
3. В рамках рабочего процесса регистрации появляется страница для сбора данных и проверки личности пользователя с использованием адреса электронной почты. Рабочий процесс регистрации также собирает пароль пользователя и запрашиваемые атрибуты, определенные в потоке пользователя.

    Используйте действительный адрес электронной почты и подтвердите его с помощью кода проверки. Задайте пароль. Введите значения запрашиваемых атрибутов.

    ![Страница регистрации, отображаемая как часть рабочего процесса входа и регистрации](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

4. Щелкните **Создать**, чтобы создать локальную учетную запись в клиенте Azure AD B2C.

Теперь пользователь может использовать свой адрес электронной почты для входа в классическое приложение и работы с ним.

> [!NOTE]
> Если нажать кнопку **вызова API**, появится сообщение об ошибке "Не авторизовано". Эта ошибка возникает, потому что вы пытаетесь получить доступ к ресурсу из демонстрационного клиента. Так как маркер доступа действителен только для вашего клиента Azure AD, вызов API считается неавторизованным. Перейдите к следующему руководству, чтобы создать защищенный веб-API для своего клиента.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * добавление собственного клиентского приложения;
> * настройка примера для использования приложения;
> * регистрация с помощью потока пользователя.

> [!div class="nextstepaction"]
> [Руководство. Предоставление доступа к веб-API Node.js из классического приложения с помощью Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
