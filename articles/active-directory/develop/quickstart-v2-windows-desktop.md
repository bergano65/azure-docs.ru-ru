---
title: Краткое руководство. Azure AD версии 2 для классического приложения Windows | Документация Майкрософт
description: Узнайте, как классическое приложение .NET для Windows (XAML) может получить маркер доступа и вызвать API, защищенный конечной точкой Azure Active Directory версии 2.0
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74f024ee55a707534fbd42b647845179baed01ce
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201047"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-windows-desktop-app"></a>Краткое руководство. Получение маркера безопасности и вызов API Microsoft Graph из классического приложения Windows

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

В этом кратком руководстве вы узнаете, как записывать классическое приложение .NET для Windows (WPF), которое может входить в личные, рабочие и школьные учетные записи, получать маркер доступа и вызывать API Microsoft Graph.

![Схема работы приложения, создаваемого в этом кратком руководстве](media/quickstart-v2-windows-desktop/windesktop-intro-updated.png)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Регистрация и скачивание приложения, используемого в этом кратком руководстве
> У вас есть два варианта запуска приложения, используемого в этом кратком руководстве:
> * [Экспресс-способ] [Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Вручную] [Вариант 2. Регистрация и настройка приложения и примера кода вручную](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода
>
> 1. Откройте [Регистрация приложений (предварительная версия)](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs) на портале Azure.
> 1. Введите имя приложения и нажмите кнопку **Зарегистрировать**.
> 1. Следуйте инструкциям, чтобы быстро скачать и автоматически настроить новое приложение.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Вариант 2. Регистрация и настройка приложения и примера кода вручную
>
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
> Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение вручную, сделайте следующее:
>
> 1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
> 1. Если учетная запись предоставляет доступ нескольким клиентам, выберите свою учетную запись в правом верхнем углу и нужный клиент Azure AD для этого сеанса портала.
> 1. В области навигации слева выберите службу **Azure Active Directory**, а затем выберите **Регистрация приложений (предварительная версия)** > **Новая регистрация**.
> 1. После появления страницы **Регистрация приложения** введите сведения о регистрации приложения:
>      - В разделе **Имя** введите понятное имя приложения, которое будет отображаться пользователям приложения, например `Win-App-calling-MsGraph`.
>      - В разделе **Поддерживаемые типы учетных записей** выберите **Accounts in any organizational directory and personal Microsoft accounts (for example, Skype, Xbox, Outlook.com)** (Учетные записи в любом каталоге организации и личные учетные записи Майкрософт (например, Skype, Xbox, Outlook.com)).
>      - Выберите **Зарегистрировать**, чтобы создать приложение.
> 1. В списке страниц приложения выберите **Проверка подлинности**.
> 1. В разделе **URI перенаправления** найдите **предлагаемые URI перенаправления для общедоступных клиентов (мобильные устройства, компьютеры)** и выберите **"urn:ietf:wg:oauth:2.0:oob**.
> 1. Щелкните **Сохранить**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Шаг 1. Настройка приложения на портале Azure
> Для работы примера кода в этом кратком руководстве необходимо добавить URL-адрес ответа, например **urn:ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести это изменение для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-windows-desktop/green-check.png). Ваше приложение настроено с помощью этих атрибутов.

#### <a name="step-2-download-your-visual-studio-project"></a>Шаг 2. Скачивание проекта Visual Studio

[Загрузить проект Visual Studio 2017](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Шаг 3. Настройка проекта Visual Studio

1. Извлеките ZIP-файл в локальную папку, расположенную как можно ближе к корню диска (например, **C:\Azure-Samples**).
1. Откройте проект в Visual Studio.
1. Измените файл **App.Xaml.cs**, заменив значения полей `ClientId` и `Tenant` следующим кодом:

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    private static string Tenant = "Enter_the_Tenant_Info_Here";
    ```

> [!div renderon="docs"]
> Описание
> - `Enter_the_Application_Id_here` — это **идентификатор приложения (клиента)**, которое вы зарегистрировали.
> - `Enter_the_Tenant_Info_Here` может иметь несколько значений.
>   - Если приложение поддерживает **учетные записи только в этом каталоге организации**, замените это значение **идентификатором клиента** или **именем клиента** (например, contoso.microsoft.com).
>   - Если ваше приложение поддерживает вариант **Учетные записи в любом каталоге организации**, замените это значение на `organizations`.
>   - Если приложение поддерживает **учетные записи в любом каталоге организации и личные учетные записи Майкрософт**, укажите значение `common`.
>
> > [!TIP]
> > Чтобы найти значения параметров **Идентификатор приложения (клиента)**, **Идентификатор каталога (клиента)** и **Поддерживаемые типы учетных записей**, на портале Azure перейдите на страницу **Обзор**.

## <a name="more-information"></a>Дополнительные сведения

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) — это библиотека, используемая для регистрации пользователей и запросов маркеров безопасности, которые используются при доступе к API, защищенному Microsoft Azure Active Directory (Azure AD). MSAL можно установить, выполнив в **консоли диспетчера пакетов** Visual Studio следующую команду.

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Инициализация MSAL

Добавив следующий код, вы можете добавить ссылку на MSAL.

```csharp
using Microsoft.Identity.Client;
```

Затем выполните инициализацию MSAL с помощью следующего кода.

```csharp
public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
```

> |Описание ||
> |---------|---------|
> | `ClientId` | **Идентификатор приложения (клиента)**, зарегистрированного на портале Azure. Это значение можно найти на странице приложения **Обзор** на портале Azure. |

### <a name="requesting-tokens"></a>Запрос маркеров

В MSAL есть два метода получения маркеров: `AcquireTokenAsync` и `AcquireTokenSilentAsync`.

#### <a name="get-a-user-token-interactively"></a>Интерактивное получение маркера пользователя

В некоторых ситуациях необходимо принудительное взаимодействие пользователя с конечной точкой Azure AD версии 2.0 через всплывающее окно, чтобы подтвердить его учетные данные или предоставить согласие. Некоторые примеры:

- первый вход пользователей в приложение;
- когда пользователям может потребоваться повторно ввести учетные данные, так как истек срок действия пароля;
- когда ваше приложение запрашивает доступ к ресурсу, на обращение к которому пользователь должен дать согласие.
- когда требуется двухфакторная проверка подлинности.

```csharp
authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
```

> |Описание||
> |---------|---------|
> | `_scopes` | Содержит запрашиваемые области (такие как `{ "user.read" }` для Microsoft Graph или `{ "api://<Application ID>/access_as_user" }` для пользовательских веб-API). |

#### <a name="get-a-user-token-silently"></a>Автоматическое получение маркера пользователя

Вы не хотите требовать от пользователя проверки своих учетных данных каждый раз, когда ему необходимо получить доступ к ресурсу. Большую часть времени вы хотите приобретать и обновлять маркеры без какого-либо взаимодействия с пользователем. Можно использовать метод `AcquireTokenSilentAsync`, чтобы получить маркеры безопасности для доступа к защищенным ресурсам после первоначального метода `AcquireTokenAsync`:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());
```

> |Описание ||
> |---------|---------|
> | `scopes` | Содержит запрашиваемые области (такие как `{ "user.read" }` для Microsoft Graph или `{ "api://<Application ID>/access_as_user" }` для пользовательских веб-API). |
> | `accounts.FirstOrDefault()` | Указывает первого пользователя в кэше (MSAL поддерживает нескольких пользователей в одном приложении). |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Дополнительная информация

В руководстве по классическому приложению Windows вы найдете пошаговые инструкции по созданию приложений и компонентов, в том числе полное описание того, о чем говорится в этом кратком руководстве.

> [!div class="nextstepaction"]
> [Руководство по вызову API Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-windesktop)

