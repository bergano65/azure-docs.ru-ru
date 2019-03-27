---
title: Краткое руководство. Универсальная платформа Windows для Azure Active Directory версии 2 | Документация Майкрософт
description: Здесь описывается, как приложение универсальной платформы Windows (XAML) может получить маркер доступа и вызвать API, защищенный конечной точкой Azure Active Directory версии 2.0.
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
ms.openlocfilehash: 97ad2c5a395eb22ec8f20d4bab3e12f1091dceb0
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200945"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Краткое руководство. Вызов API Microsoft Graph из приложения для универсальной платформы Windows (UWP)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

В этом кратком руководстве содержится пример кода, который демонстрирует, как приложение универсальной платформы Windows (UWP) может входить от имени пользователей в личные, рабочие, а также учебные учетные записи, получать маркер доступа и вызывать API Microsoft Graph.

![Схема работы приложения, создаваемого в этом кратком руководстве](media/quickstart-v2-uwp/uwp-intro-updated.png)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Регистрация и скачивание приложения, используемого в этом кратком руководстве
> [!div renderon="docs" class="sxs-lookup"]
> У вас есть два варианта запуска приложения, используемого в этом кратком руководстве:
> * [Экспресс-способ] [Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Вручную] [Вариант 2. Регистрация и настройка приложения и примера кода вручную](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Вариант 1. Регистрация и автоматическая настройка приложения, а затем скачивание примера кода
>
> 1. Откройте [Регистрация приложения](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps) на портале Azure.
> 1. Введите имя своего приложения и щелкните **Зарегистрировать**.
> 1. Следуйте инструкциям для загрузки и автоматической настройки нового приложения одним щелчком мыши.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Вариант 2. Регистрация и настройка приложения и примера кода вручную
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
> Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение, сделайте следующее:
> 1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
> 1. Если учетная запись предоставляет доступ нескольким клиентам, выберите свою учетную запись в правом верхнем углу и нужный клиент Azure AD для этого сеанса портала.
> 1. В области навигации слева выберите службу **Azure Active Directory**, а затем выберите **Регистрация приложений (предварительная версия)** > **Новая регистрация**.
> 1. После появления страницы **Регистрация приложения** введите сведения о регистрации приложения:
>      - В разделе **Имя** введите понятное имя приложения, которое будет отображаться пользователям приложения, например `UWP-App-calling-MsGraph`.
>      - В разделе **Поддерживаемые типы учетных записей** выберите **Accounts in any organizational directory and personal Microsoft accounts (for example, Skype, Xbox, Outlook.com)** (Учетные записи в любом каталоге организации и личные учетные записи Майкрософт (например, Skype, Xbox, Outlook.com)).
>      - Выберите **Зарегистрировать**, чтобы создать приложение.
> 1. В списке страниц приложения выберите **Проверка подлинности**.
> 1. В разделе **URI перенаправления** найдите **предлагаемые URI перенаправления для общедоступных клиентов (мобильные устройства, компьютеры)** и выберите **"urn:ietf:wg:oauth:2.0:oob**.
> 1. Щелкните **Сохранить**.

> [!div renderon="portal" class="sxs-lookup alert alert-info"]
> #### <a name="step-1-configure-your-application"></a>Шаг 1. Настройка приложения
> Для работы примера кода в этом кратком руководстве необходимо добавить URI перенаправления в виде **urn:ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести это изменение для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-uwp/green-check.png). Ваше приложение настроено с помощью этих атрибутов.

#### <a name="step-2-download-your-visual-studio-project"></a>Шаг 2. Скачивание проекта Visual Studio

 - [Загрузить проект Visual Studio 2017](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip)

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
> - `Enter_the_Application_Id_here` — идентификатор регистрируемого приложения.
> - `Enter_the_Tenant_Info_Here` — один из следующих вариантов:
>   - Если ваше приложение поддерживает вариант **Только моя организация**, замените это значение на **идентификатор клиента** или **имя клиента** (например, contoso.microsoft.com).
>   - Если ваше приложение поддерживает вариант **Учетные записи в любом каталоге организации**, замените это значение на `organizations`.
>   - Если приложение поддерживает вариант **Все пользователи с учетными записями Майкрософт**, замените это значение на `common`.
>
> > [!TIP]
> > Чтобы найти значения параметров *Идентификатор приложения*, *Идентификатор каталога (клиента)* и *Поддерживаемые типы учетных записей*, перейдите на страницу **Обзор**.

## <a name="more-information"></a>Дополнительные сведения

Этот раздел содержит дополнительные сведения о работе с этим кратким руководством.

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) — это библиотека, используемая для входа пользователей и запросов маркеров, используемых для доступа к API, защищенному Microsoft Azure Active Directory. MSAL можно установить, выполнив в *консоли диспетчера пакетов* Visual Studio следующую команду.

```powershell
Install-Package Microsoft.Identity.Client -Pre
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
authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
```

> |Описание||
> |---------|---------|
> | `scopes` | Содержит запрашиваемые области (такие как `{ "user.read" }` для Microsoft Graph или `{ "api://<Application ID>/access_as_user" }` для пользовательских веб-API). |

#### <a name="get-a-user-token-silently"></a>Автоматическое получение маркера пользователя

Вы не хотите требовать от пользователя проверки своих учетных данных каждый раз, когда ему необходимо получить доступ к ресурсу. Большую часть времени вы хотите приобретать и обновлять маркеры без какого-либо взаимодействия с пользователем. Можно использовать метод `AcquireTokenSilentAsync`, чтобы получить маркеры безопасности для доступа к защищенным ресурсам после первоначального метода `AcquireTokenAsync`:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());
```

> |Описание ||
> |---------|---------|
> | `scopes` | Содержит запрашиваемые области (такие как `{ "user.read" }` для Microsoft Graph или `{ "api://<Application ID>/access_as_user" }` для пользовательских веб-API) |
> | `accounts.FirstOrDefault()` | Указывает первого пользователя в кэше (MSAL поддерживает нескольких пользователей в одном приложении) |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Дополнительная информация

В руководстве по классическому приложению Windows вы найдете пошаговые инструкции по созданию приложений и компонентов, в том числе полное описание того, о чем говорится в этом кратком руководстве.

> [!div class="nextstepaction"]
> [Вызов API Microsoft Graph из приложения для универсальной платформы Windows (XAML)](tutorial-v2-windows-uwp.md)
