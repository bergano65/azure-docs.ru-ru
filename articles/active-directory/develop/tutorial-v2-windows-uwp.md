---
title: Приступая к работе с платформой удостоверений Майкрософт и универсальной платформой Windows | Azure
description: В этой статье описано, как приложения для универсальной платформы Windows (UWP) могут вызвать API, которому требуются маркеры доступа от конечной точки платформы удостоверений Майкрософт.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 519a86bed6a3a09b476bce6435ae666d655dbe03
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852265"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Вызов API Microsoft Graph из приложения для универсальной платформы Windows (XAML)

> [!div renderon="docs"]

В этом руководстве объясняется, как собственное приложение универсальной платформы Windows (UWP) может запросить маркер доступа и затем вызвать API Microsoft Graph. Руководство также применимо к другим API, которым требуются маркеры доступа от конечной точки платформы удостоверений Майкрософт.

В конце этого руководства приложение вызывает защищенный API-интерфейс с использованием личных учетных записей, таких как outlook.com, live.com и другие. Приложение также вызывает рабочие и учебные учетные записи из любой компании или организации, использующей Azure Active Directory (Azure AD).

>[!NOTE]
> Для работы с этим руководством требуется Visual Studio 2017 с установленным компонентом "Разработка приложений для универсальной платформы Windows". Ознакомьтесь со статьей о [настройке](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) для получения инструкций по скачиванию и настройке Visual Studio для разработки приложений для универсальной платформы Windows.

## <a name="how-this-guide-works"></a>Принцип работы с руководством

![Схема работы примера приложения, создаваемого в этом кратком руководстве](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

В этом руководстве создается пример приложения UWP, которое выполняет запрос к API Microsoft Graph или веб-API, принимающему маркеры от конечной точки платформы удостоверений Майкрософт. В этом сценарии маркер добавляется в запросы HTTP с помощью заголовка авторизации. Получение и продление маркеров выполняет библиотека проверки подлинности Майкрософт (MSAL).

## <a name="nuget-packages"></a>Пакеты NuGet

В этом руководстве используются следующие пакеты NuGet:

|Библиотека|ОПИСАНИЕ|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Библиотека проверки подлинности Майкрософт|

## <a name="set-up-your-project"></a>Настройка проекта

В этом разделе содержатся пошаговые инструкции по интеграции классического приложения .NET для Windows (XAML) с функцией *входа с учетной записью Майкрософт*. Затем приложение может выполнить запрос к веб-API, которым требуется маркер, например API Microsoft Graph.

В этом руководстве создается приложение, в котором отображается кнопка для запроса API Graph, кнопка выхода и текстовые поля с результатами вызовов.

> [!NOTE]
> Предпочитаете скачать этот пример проекта Visual Studio? [Скачайте проект](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) и перейдите к шагу [регистрации приложения](#register-your-application "application registration step"), чтобы настроить пример кода перед выполнением.

### <a name="create-your-application"></a>Создание приложения

1. В Visual Studio выберите **Файл** > **Создать** > **Проект**.
2. В разделе **Шаблоны** выберите **Visual C#** .
3. Выберите **Пустое приложение (универсальное приложение Windows)** .
4. Присвойте приложению имя и щелкните **ОК**.
5. Когда появится соответствующий запрос, выберите любую **целевую** и **минимальную** версии и нажмите кнопку **ОК**.

    >![Минимальная и целевая версии](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Добавление библиотеки проверки подлинности Майкрософт в проект
1. В Visual Studio выберите **Сервис** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.
2. Скопируйте и вставьте следующую команду в окно **консоли диспетчера пакетов**:

    ```powershell
    Install-Package Microsoft.Identity.Client -IncludePrerelease
    ```

> [!NOTE]
> Эта команда устанавливает [библиотеку проверки подлинности Майкрософт](https://aka.ms/msal-net). MSAL получает, кэширует и обновляет маркеры пользователей для доступа к API, защищенным с помощью платформы удостоверений Майкрософт.

## <a name="create-your-applications-ui"></a>Создание пользовательского интерфейса приложения

Файл **MainPage.xaml** создается автоматически как часть шаблона проекта. Откройте этот файл и выполните инструкции:

* Замените узел **Grid** вашего приложения следующим кодом:

    ```xml
    <Grid>
        <StackPanel Background="Azure">
            <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
                <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
                <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
            </StackPanel>
            <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
            <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
        </StackPanel>
    </Grid>
    ```
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Получение маркера для API Microsoft Graph с помощью MSAL

В этом разделе показано, как с помощью MSAL получить маркер для API Microsoft Graph.

1.  В файле **MainPage.xaml.cs** добавьте ссылку на библиотеку MSAL в класс:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Замените код класса <code>MainPage</code> следующим кодом:

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - Te content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use organizations
        //   - for any Work or School accounts, or Microsoft personal account, use common
        //   - for Microsoft Personal account, use consumers
        private const string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";        

        public IPublicClientApplication PublicClientApp { get; } 

        public MainPage()
        {
          this.InitializeComponent();

          PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount)
                .WithLogging((level, message, containsPii) =>
                {
                    Debug.WriteLine($"MSAL: {level} {message} ");
                }, LogLevel.Warning, enablePiiLogging:false,enableDefaultPlatformLogging:true)
                .WithUseCorporateNetwork(true)
                .Build();
        }

        /// <summary>
        /// Call AcquireTokenInteractive - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
         AuthenticationResult authResult = null;
         ResultText.Text = string.Empty;
         TokenInfoText.Text = string.Empty;

         // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.            
         IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false); 
         IAccount firstAccount = accounts.FirstOrDefault();

         try
         {
          authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
         }
         catch (MsalUiRequiredException ex)
         {
          // A MsalUiRequiredException happened on AcquireTokenSilent.
          // This indicates you need to call AcquireTokenInteractive to acquire a token
          System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

          try
          {
           authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                      .ExecuteAsync()
                                                      .ConfigureAwait(false);
           }
           catch (MsalException msalex)
           {
            await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalex}");
           }
          }
          catch (Exception ex)
          {
           await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
           return;
          }

          if (authResult != null)
          {
           var content = await GetHttpContentWithToken(graphAPIEndpoint,
                                                       authResult.AccessToken).ConfigureAwait(false);

           // Go back to the UI thread to make changes to the UI
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
            ResultText.Text = content;
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
           });
          }
        }
    ```

### <a name="more-information"></a>Дополнительные сведения

#### <a name="get-a-user-token-interactively"></a>Интерактивное получение маркера пользователя

При вызове метода `AcquireTokenInteractive` появится окно, в котором пользователю предлагается выполнить вход. Когда пользователь впервые запрашивает доступ к защищенному ресурсу, приложение обычно требует выполнить интерактивный вход. Пользователям также может потребоваться выполнить вход при сбое автоматической операции получения маркера, например, если истек срок действия пароля пользователя.

#### <a name="get-a-user-token-silently"></a>Автоматическое получение маркера пользователя

Метод `AcquireTokenSilent` обрабатывает получение и обновление маркера без участия пользователя. После того как `AcquireTokenInteractive` выполнится в первый раз и пользователю будет предложено ввести учетные данные, следует использовать метод `AcquireTokenSilent` для запроса маркеров для последующих вызовов, так как он получает маркеры автоматически. Кэшированием и обновлением маркеров займется MSAL.

Иногда метод `AcquireTokenSilent` завершается ошибкой. Причина может заключаться в том, что пользователь вышел из системы или изменил пароль на другом устройстве. Когда MSAL обнаруживает, что эту проблему можно решить, запросив интерактивное действие, возникает исключение `MsalUiRequiredException`. Приложение может обработать это исключение двумя способами:

* Может немедленно вызвать `AcquireTokenInteractive`. Этот вызов приводит к появлению запроса на вход пользователя. Этот шаблон обычно используется в интернет-приложениях, где пользователю недоступно автономное содержимое. Пример, созданный в ходе пошаговой настройки, следует этому шаблону. Вы увидите его в действии при первом запуске примера.
  * Так как приложение еще не использовалось, `accounts.FirstOrDefault()` будет содержать значение NULL и будет выдано исключение `MsalUiRequiredException`.
  * Затем код в примере обрабатывает исключение, вызывая `AcquireTokenInteractive`. Этот вызов приводит к появлению запроса на вход пользователя.

* Или вместо этого он демонстрирует пользователям визуальное оповещение о том, что требуется интерактивный вход. После этого пользователи могут выбрать нужное время для входа. Либо приложение может попытаться повторно выполнить метод `AcquireTokenSilent` позже. Зачастую этот шаблон применяется, когда пользователи могут использовать другие функциональные возможности приложения без прерывания его работы. Например, когда в приложении доступно автономное содержимое. В этом случае пользователи могут решать, что им нужно сделать после входа — получить доступ к защищенному ресурсу или обновить устаревшие данные. Или же приложение может попытаться повторно выполнить `AcquireTokenSilent` при восстановлении подключения к сети после временной недоступности.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Вызов API Microsoft Graph с помощью полученного маркера

* Добавьте приведенный ниже новый метод в файл **MainPage.xaml.cs**. Этот метод используется для выполнения запроса `GET` к API Graph с помощью заголовка авторизации `Authorization`:

    ```csharp
    /// <summary>
    /// Perform an HTTP GET request to a URL using an HTTP Authorization header
    /// </summary>
    /// <param name="url">The URL</param>
    /// <param name="token">The token</param>
    /// <returns>String containing the results of the GET operation</returns>
    public async Task<string> GetHttpContentWithToken(string url, string token)
    {
        var httpClient = new System.Net.Http.HttpClient();
        System.Net.Http.HttpResponseMessage response;
        try
        {
            var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
            // Add the token in Authorization header
            request.Headers.Authorization = 
              new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
            response = await httpClient.SendAsync(request);
            var content = await response.Content.ReadAsStringAsync();
            return content;
        }
        catch (Exception ex)
        {
            return ex.ToString();
        }
    }
    ```

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Дополнительные сведения о вызове REST через защищенный API

В этом примере приложения метод `GetHttpContentWithToken` выполняет HTTP-запрос `GET` к защищенному ресурсу, которому требуется маркер. Затем метод возвращает содержимое вызывающему объекту. Этот метод добавляет полученный маркер в заголовок **авторизации HTTP**. В этом примере ресурс — это конечная точка **me** API Microsoft Graph, которая отображает сведения о профиле пользователя.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Добавление метода для выхода пользователя

* Для выхода пользователя добавьте следующий метод в файл **MainPage.xaml.cs**:

    ```csharp
    /// <summary>
    /// Sign out the current user
    /// </summary>
    private async void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync
                                                              .ConfigureAwait(false);
        IAccount firstAccount = accounts.FirstOrDefault();

        try
        {
            await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
            await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                ResultText.Text = "User has signed-out";
                this.CallGraphButton.Visibility = Visibility.Visible;
                    this.SignOutButton.Visibility = Visibility.Collapsed;
                });
            }
            catch (MsalException ex)
            {
                ResultText.Text = $"Error signing-out user: {ex.Message}";
            }
        }
    ```

> [!NOTE]
> MSAL.NET использует асинхронные методы для получения маркеров и управления учетными записями. Поэтому следует внимательно выполнять операции с помощью пользовательского интерфейса в цепочке действий, т. е. `Dispatcher.RunAsync`, и принимать меры предосторожности при вызове `ConfigureAwait(false)`.

### <a name="more-information-on-sign-out"></a>Дополнительные сведения о выходе

Метод `SignOutButton_Click` удаляет пользователя из кэша пользователей MSAL. Этот метод фактически указывает MSAL забыть текущего пользователя. Затем будущий запрос получения маркера завершается успешно только в том случае, если он реализован как интерактивный.
Приложение в этом примере поддерживает одиночного пользователя. Но MSAL поддерживает сценарии, где одновременно можно выполнить вход с помощью нескольких учетных записей. Пример — приложение электронной почты, в котором у пользователя есть несколько учетных записей.

## <a name="display-basic-token-information"></a>Отображение основных сведений о маркере

* Добавьте следующий метод в файл **MainPage.xaml.cs** для отображения основных сведений о маркере:

    ```csharp
    /// <summary>
    /// Display basic information contained in the token. Needs to be called from the UI thead.
    /// </summary>
    private void DisplayBasicTokenInfo(AuthenticationResult authResult)
    {
        TokenInfoText.Text = "";
        if (authResult != null)
        {
            TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
            TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        }
    }
    ```

### <a name="more-information"></a>Дополнительные сведения

Маркеры идентификаторов, полученные через **OpenID Connect**, также содержат небольшой набор данных, относящихся к пользователю. `DisplayBasicTokenInfo` отображает базовые сведения, содержащиеся в маркере, например отображаемое имя и идентификатор пользователя, срок действия маркера и строка, представляющая сам маркер доступа. Если нажать кнопку **Call Microsoft Graph API** (Вызвать API Microsoft Graph) несколько раз, вы увидите, что для последующих запросов повторно используется тот же маркер. Также можно увидеть, что библиотека MSAL продлила срок действия маркера.

## <a name="register-your-application"></a>Регистрация приложения

Теперь вам необходимо зарегистрировать приложение на портале регистрации приложений Майкрософт:

1. Войдите на [портал Azure](https://portal.azure.com) с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи.
1. Если ваша учетная запись присутствует в нескольких клиентах Azure AD, выберите `Directory + Subscription` в правом верхнем углу в меню, расположенном вверху страницы, и переключите сеанс работы с порталом на нужный клиент Azure AD.
1. Перейдите на страницу [Регистрация приложений](https://go.microsoft.com/fwlink/?linkid=2083908) Платформы удостоверений Майкрософт для разработчиков.
1. Выберите **Новая регистрация**.
   - В разделе **Имя** введите понятное имя приложения, которое будет отображаться пользователям приложения, например `UWP-App-calling-MSGraph`.
   - В разделе **Поддерживаемые типы учетных записей** выберите **Учетные записи в любом каталоге организации и личные учетные записи Майкрософт (например, Skype, Xbox, Outlook.com)** .
   - Выберите **Зарегистрировать**, чтобы создать приложение.
1. На странице приложения **Обзор** найдите **идентификатор приложения (клиента)** и запишите его, чтобы использовать позже. Вернитесь в Visual Studio, откройте файл **App.xaml.cs** и замените значение ClientId только что зарегистрированным идентификатором приложения.
1. В списке страниц приложения выберите **Проверка подлинности**.
   1. В разделе **URI перенаправления** в списке URI перенаправления сделайте следующее:
   1. В столбце **Тип** выберите **Общедоступный клиент (мобильный и классический)** .
   1. Введите `urn:ietf:wg:oauth:2.0:oob` в столбец **URI перенаправления**.
1. Щелкните **Сохранить**.
1. Из списка страниц приложения выберите **Разрешения API**.
   - Нажмите кнопку **Добавить разрешение**.
   - Убедитесь, что вкладка **Microsoft API's** (Интерфейсы API (Майкрософт)) выбрана.
   - В разделе *Часто используемые интерфейсы API Microsoft* щелкните **Microsoft Graph**.
   - В разделе **Делегированные разрешения** убедитесь, что выбраны нужные разрешения: **User.Read**. При необходимости используйте поле поиска.
   - Нажмите кнопку **Добавить разрешения**.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Включение встроенной проверки подлинности в федеративных доменах (необязательно)

Чтобы включить встроенную проверку подлинности Windows при использовании с федеративным доменом Azure AD, необходимо включить дополнительные возможности в манифесте приложения.

1. Дважды щелкните **Package.appxmanifest**.
2. Выберите вкладку **Возможности** и убедитесь, что включены следующие параметры:

    - Корпоративная проверка подлинности
    - Частные сети (клиент и сервер)
    - Общие сертификаты пользователей

> [!IMPORTANT]
> [Встроенная проверка подлинности Windows](https://aka.ms/msal-net-iwa) не настроена по умолчанию для этого примера. Приложениям, запрашивающим возможности *Корпоративная проверка подлинности* или *Общие сертификаты пользователей*, требуется более высокий уровень проверки в Магазине Windows. Кроме того, не все разработчики желают выполнять проверку более высокого уровня. Этот параметр следует включать только в том случае, если требуется встроенная проверка подлинности Windows с федеративным доменом Azure AD.

## <a name="test-your-code"></a>Тестирование кода

Чтобы протестировать приложение, нажмите клавишу F5 для запуска проекта в Visual Studio. Откроется главное окно:

![Пользовательский интерфейс приложения](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Когда будете готовы выполнить тестирование, выберите **Call Microsoft Graph API** (вызвать API Graph Microsoft). Затем используйте рабочую или учебную учетную запись Azure AD либо учетную запись Майкрософт (live.com, outlook.com) для входа. Если вход выполняется впервые, появится окно с предложением войти в систему:

![Страница входа](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Согласие на предоставление разрешений

При первом входе в приложение появится экран согласия, подобный показанному ниже. Выберите **Да**, чтобы дать явное согласие на доступ:

![Экран согласия на доступ](./media/tutorial-v2-windows-uwp/consentscreen.png)

### <a name="expected-results"></a>Ожидаемые результаты

На экране **API Call Results** (Результаты вызова API) должны отобразиться сведения о профиле пользователя, возвращенные вызовом API Microsoft Graph:

![Экран результатов вызова API](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

Вы также увидите основные сведения о маркере, полученные с помощью `AcquireTokenInteractive` или `AcquireTokenSilent`, в окне **Token Info** (Сведения о маркере):

|Свойство  |Формат  |ОПИСАНИЕ |
|---------|---------|---------|
|**Имя пользователя** |<span>user@domain.com</span> |Имя пользователя, которое позволяет его идентифицировать.|
|**Истечение срока действия маркера** |Дата и время |Это время, когда истекает срок действия маркера. MSAL продлевает срок действия, по мере необходимости обновляя маркер.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Дополнительные сведения об областях и делегированных разрешениях

Для чтения профиля пользователя API Microsoft Graph требуется область *user.read*. По умолчанию эта область автоматически добавляется в каждое приложение, зарегистрированное на портале регистрации приложений. Для других API Microsoft Graph, а также для пользовательских API вашего внутреннего сервера могут потребоваться дополнительные области. Для отображения списка календарей пользователя API Microsoft Graph требуется область *Calendars.Read*.

Чтобы из контекста приложения получить доступ к календарям пользователя, добавьте делегированное разрешение *Calendars.Read* в сведения о регистрации приложения. Затем добавьте область *Calendars.Read* в вызов `acquireTokenSilent`.

> [!NOTE]
> При увеличении количества областей от пользователя могут потребоваться дополнительные согласия.

## <a name="known-issues"></a>Известные проблемы

### <a name="issue-1"></a>Проблема 1

При входе в приложение в федеративном домене Azure AD появляется одно из следующих сообщений об ошибке:

* No valid client certificate found in the request (В запросе отсутствует действительный клиентский сертификат).
* No valid certificates found in the user's certificate store (В хранилище сертификатов пользователя отсутствуют действительные сертификаты).
* Попробуйте еще раз, выбрав другой метод проверки подлинности.

**Причина.** Не включены возможности корпоративного уровня и сертификатов.

**Решение.** Выполните действия из раздела о [встроенной аутентификации в федеративных доменах](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Проблема 2

Вы включили [встроенную проверку подлинности в федеративных доменах](#enable-integrated-authentication-on-federated-domains-optional) и пытаетесь использовать Windows Hello на компьютере под управлением Windows 10 для входа в среду с настроенной многофакторной проверкой подлинности. Отображается список сертификатов. Однако если вы решили использовать ПИН-код, окно ПИН-кода никогда не отображается.

**Причина.** Эта проблема вызвана известным ограничением веб-брокера аутентификации в приложениях UWP, запускаемых на рабочем столе Windows 10. В Windows 10 Mobile он работает нормально.

**Возможное решение.** Выберите **Sign in with other options** (Вход с другими параметрами). Затем выберите **Sign in with a username and password** (Вход с использованием имени пользователя и пароля). Выберите **Provide your password** (Ввести пароль). После этого пройдите процесс проверки подлинности по телефону.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Помогите нам улучшить платформу Microsoft Identity. Поделитесь своим мнением, ответив на два вопроса.

> [!div class="nextstepaction"]
> [Опрос по платформе удостоверений Майкрософт](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
