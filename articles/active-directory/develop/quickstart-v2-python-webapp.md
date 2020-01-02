---
title: Добавление единого входа в веб-приложение Python платформы удостоверений Майкрософт
description: Узнайте, как реализовать единый вход Майкрософт на веб-приложении Python с помощью OAuth2
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: 8a6e8dad7549b3656d4387cfad1d0c3a44b13975
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965982"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Краткое руководство. Добавление возможности входа в веб-приложение Python с помощью учетной записи Майкрософт

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

В этом кратком руководстве вы узнаете, как интегрировать веб-приложение Python с Платформой удостоверений Майкрософт. Ваше приложение сможет авторизовать пользователя, получить маркер доступа для вызова API Microsoft Graph и выполнить запрос к API Microsoft Graph.

Когда вы завершите работу с этим руководством, ваше приложение сможет принимать операции входа с помощью личных учетных записей Майкрософт (включая outlook.com, live.com и другие), а также рабочих или учебных учетных записей из любой компании или организации, в которых используется Azure Active Directory.

![Схема работы приложения, создаваемого в этом кратком руководстве](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>Предварительные требования

Для запуска этого примера потребуется:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) или [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/), [Flask-сеанс](https://pythonhosted.org/Flask-Session/), [запросы](https://requests.kennethreitz.org//en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Регистрация и скачивание приложения, используемого в этом кратком руководстве
>
> У вас есть два варианта запуска приложения, используемого в этом кратком руководстве: оперативно (вариант 1) и вручную (вариант 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода
>
> 1. Откройте [Регистрация приложений](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) на портале Azure.
> 1. Выберите **Новая регистрация**.
> 1. Введите имя приложения и нажмите кнопку **Зарегистрировать**.
> 1. Следуйте инструкциям, чтобы быстро скачать и автоматически настроить новое приложение.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Вариант 2. Регистрация и настройка приложения и примера кода вручную
>
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
>
> Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение вручную, сделайте следующее:
>
> 1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
> 1. Если учетная запись предоставляет доступ нескольким клиентам, выберите свою учетную запись в правом верхнем углу и нужный клиент Azure AD для этого сеанса портала.
> 1. Перейдите на страницу [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) Платформы удостоверений Майкрософт для разработчиков.
> 1. Выберите **Новая регистрация**.
> 1. После появления страницы **Регистрация приложения** введите сведения о регистрации приложения:
>      - В разделе **Имя** введите понятное имя приложения, которое будет отображаться пользователям приложения, например `python-webapp`.
>      - В разделе **Поддерживаемые типы учетных записей** выберите **Accounts in any organizational directory and personal Microsoft accounts** (Учетные записи в любом каталоге организации и личные учетные записи Майкрософт).
>      - В разделе **URI перенаправления** в раскрывающемся списке выберите **Веб-платформа**, а затем задайте значение `http://localhost:5000/getAToken`.
>      - Выберите **Зарегистрировать**. На странице приложения **Обзор** запишите **идентификатор приложения (клиента)** для использования в будущем.
> 1. В левом меню выберите **Сертификаты и секреты** и выберите **Новый секрет клиента** в разделе **Секреты клиента**:
>
>      - Введите описание ключа (экземпляр секрета приложения).
>      - Выберите срок действия ключа**в 1 год**.
>      - При нажатии кнопки **Добавить** будет отображаться значение ключа.
>      - Скопируйте значение ключа. Оно понадобится вам позднее.
> 1. Выберите раздел **Разрешения API**.
>
>      - Нажмите кнопку **Добавить разрешение**.
>      - Убедитесь, что вкладка **API Майкрософт** выбрана.
>      - В разделе *Часто используемые интерфейсы API Microsoft* щелкните **Microsoft Graph**.
>      - В разделе **Делегированные разрешения** убедитесь, что выбраны нужные разрешения: **User.ReadBasic.All**. При необходимости используйте поле поиска.
>      - Нажмите кнопку **Добавить разрешения**.
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Шаг 1. Настройка приложения на портале Azure
>
> Для работы примера кода в этом кратком руководстве необходимо:
>
> 1. Добавьте URL-адрес для ответа как`http://localhost:5000/getAToken`.
> 1. Создание Секрета клиента.
> 1. Добавьте делегированное разрешение API Microsoft Graph User.ReadBasic.All.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести эти изменения для меня]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-aspnet-webapp/green-check.png) Ваше приложение настроено с использованием этого атрибута

#### <a name="step-2-download-your-project"></a>Шаг 2. Скачивание проекта

[Скачайте пример кода.](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>Шаг 3. Настройка приложения

1. Извлеките ZIP-файл в локальную папку, расположенную как можно ближе к корневой папке (например, **C:\Azure-Samples**).
1. Если вы используете интегрированную среду разработки, откройте образец в вашей избранной интегрированной среде разработки (необязательно).
1. Откройте файл **app_config.py**, который можно найти в корневой папке, и замените его следующим фрагментом кода:

```python
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
```

> [!div renderon="docs"]
> Описание
>
> - `Enter_the_Application_Id_here` — идентификатор регистрируемого приложения.
> - `Enter_the_Client_Secret_Here` — это **Секрет клиента**, созданного вами в пункте **Сертификаты и Секреты** для зарегистрированного приложения.
> - `Enter_the_Tenant_Name_Here` — это значение **Идентификатор каталога (клиента)** приложения, которое вы зарегистрировали.

#### <a name="step-4-run-the-code-sample"></a>Шаг 4. Запуск примера кода

1. Вам потребуется установить библиотеку Python MSAL, Flask Framework, Flask-Sessions для управления сеансами на стороне сервера и выполнения запросов с помощью PIP следующим образом:

   ```Shell
   pip install -r requirements.txt
   ```

2. Запустите "app.py" из оболочки или командной строки:

   ```Shell
   python app.py
   ```
   > [!IMPORTANT]
   > В этом кратком руководстве приложение использует секрет клиента для собственной идентификации в качестве конфиденциального клиента. Так как секрет клиента добавляется в качестве обычного текста в файлы проекта, из соображениям безопасности рекомендуется использовать сертификат вместо секрета клиента, прежде чем использовать приложение в качестве рабочего. Дополнительные сведения о том, как использовать сертификат, можно найти в [этих инструкциях](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Дополнительные сведения

### <a name="getting-msal"></a>Получение MSAL
MSAL — это библиотека, используемая для выполнения входа пользователей и запроса маркеров, которые нужны для доступа к API, защищенному платформой удостоверений Майкрософт.
MSAL для Python можно добавить в приложение с помощью Pip.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Инициализация MSAL
Вы можете добавить ссылку на MSAL для Python, добавив следующий код в начало файла, в котором будет использоваться MSAL:

```Python
import msal
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о веб-приложениях для входа пользователей в систему и вызова веб-API

> [!div class="nextstepaction"]
> [Scenario: Веб-приложения для входа пользователей в систему](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
