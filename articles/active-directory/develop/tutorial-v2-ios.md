---
title: Начало работы с iOS и macOS на платформе удостоверений Майкрософт | Azure
description: Как приложения iOS и macOS (Swift) могут вызывать API, которому необходимы маркеры доступа, используя платформу удостоверений Майкрософт
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64f161d94d622ae76932e88be52df6f068bfc945
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964674"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Вход пользователей и вызов Microsoft Graph из приложения iOS или macOS

Из этого руководства вы узнаете, как интегрировать приложение iOS или macOS в платформу удостоверений Майкрософт. Приложение сможет авторизовать пользователя, получить маркер доступа для вызова API Microsoft Graph и выполнить запрос к API Microsoft Graph.  

Когда вы завершите работу с этим руководством, ваше приложение сможет принимать операции входа с помощью личных учетных записей Майкрософт (включая outlook.com, live.com и другие), а также рабочих или учебных учетных записей из любой компании или организации, в которых используется Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Как работает это руководство

![Схема работы примера приложения, создаваемого в этом кратком руководстве](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

В этом руководстве приложение авторизует пользователя и получает данные от его имени.  Доступ к этим данным будет осуществляться через защищенный API (в данном случае API Microsoft Graph), который требует авторизации и защищен платформой удостоверений Майкрософт.

В частности:

* Приложение авторизует пользователя через браузер или Microsoft Authenticator.
* Пользователь принимает разрешения, которые запрашивает приложение.
* Приложение выдает маркер доступа для API Microsoft Graph.
* Этот маркер доступа будет включен в HTTP-запрос к веб-API.
* Затем обрабатывается ответ Microsoft Graph.

В этом примере используется библиотека проверки подлинности Майкрософт (MSAL) для реализации аутентификации. MSAL автоматически обновляет маркеры, обеспечивает единый вход для других приложений на устройстве, а также управляет учетными записями.

Этот учебник применим к приложениям iOS и macOS. Обратите внимание, что некоторые шаги для этих двух платформ отличаются. 

## <a name="prerequisites"></a>Предварительные требования

- Для сборки приложения в этом руководстве требуется XCode версии 10.x или более поздней. Скачать XCode можно с [сайта iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL-адрес скачивания XCode").
- Библиотека проверки подлинности Майкрософт ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Вы можете использовать диспетчер зависимостей или добавить библиотеку вручную. В инструкциях ниже описано как это сделать.

В рамках этого руководства будет создан проект. Если вместо этого вы хотите скачать готовое руководство, скачайте код:
- [Пример кода для iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Пример кода для macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>Создание нового проекта

1. Запустите Xcode и щелкните **Create a new Xcode project** (Создать новый проект Xcode).
2. Для приложений iOS выберите **iOS** > **Приложение с одним представлением**, а затем **Далее**.
3. Для приложений macOS выберите **macOS** > **Cocoa App**, а затем **Далее**.
4. Укажите название продукта.
5. Установите для параметра **Язык** значение **Swift** и выберите **Далее**.
6. Выберите папку для создания приложения и нажмите кнопку **Создать**.

## <a name="register-your-application"></a>Регистрация приложения

1. Перейдите на [портал Azure](https://aka.ms/MobileAppReg).
2. На панели [Регистрация приложений](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) щелкните **+Новая регистрация**.
3. Введите **имя** приложения, а затем, не указывая URI перенаправления, щелкните **Зарегистрировать**.
4. На панели в открывшемся разделе **Управление** выберите **Аутентификация**.

5. Щелкните **Попробовать новый пользовательский интерфейс** в верхней части экрана, чтобы открыть новую процедуру регистрации приложения, а затем щелкните **+ Новая регистрация** >  **+ Добавить платформу** > **iOS**.
    - Введите идентификатор пакета проекта. Если вы скачали код, это будет `com.microsoft.identitysample.MSALiOS`. Если вы создаете собственный проект, выберите свой проект в Xcode и откройте вкладку **Общие сведения**. Идентификатор пакета появляется в разделе **Удостоверение**.
    - Обратите внимание, что для macOS также следует использовать интерфейс iOS. 
6. Щелкните `Configure` и сохраните **конфигурацию MSAL**, отображаемую на странице **Настройка iOS**, чтобы указать ее при последующей настройке приложения.  Нажмите кнопку **Done**(Готово).

## <a name="add-msal"></a>Добавление MSAL

Выберите один из следующих способов установки библиотеки MSAL в приложении:

### <a name="cocoapods"></a>CocoaPods

1. Если вы используете [CocoaPods](https://cocoapods.org/), установите `MSAL`, сначала создав пустой файл `podfile` в папке проекта `.xcodeproj`. Добавьте следующую строку в файл `podfile`:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Замените `<your-target-here>` именем проекта.
3. В окне терминала перейдите в папку, где находится `podfile`, и запустите `pod install`, чтобы установить библиотеку MSAL.
4. Чтобы перезагрузить проект в XCode закройте его и откройте `<your project name>.xcworkspace`.

### <a name="carthage"></a>Carthage

Если вы используете [Carthage](https://github.com/Carthage/Carthage), установите библиотеку `MSAL`, добавив ее в `Cartfile`:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

В окне терминала в том же каталоге, в котором находится обновленный `Cartfile`, выполните следующую команду для обновления Carthage зависимостей в проекте.

iOS:

```bash
carthage update --platform iOS
```

macOS:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Вручную

Установить библиотеку также можно с помощью подмодуля Git. Кроме того, вы можете извлечь последний выпуск чтобы использовать его в качестве платформы в своем приложении.

## <a name="add-your-app-registration"></a>Добавление регистрации приложения

Затем добавьте регистрацию приложения в код. 

Сначала добавьте в начало файлов `ViewController.swift` и `AppDelegate.swift` следующие инструкцию import:

```swift
import MSAL
```

Затем добавьте следующий код в `ViewController.swift` до `viewDidLoad()`.

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/" // the Microsoft Graph endpoint
let kScopes: [String] = ["https://graph.microsoft.com/user.read"] // request permission to read the profile of the signed-in user
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
```

Единственное значение выше, которое необходимо изменить, — это значение, присваиваемое `kClientID` в качестве [идентификатора приложения](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id). Это значение является частью данных конфигурации MSAL, сохраненного на шаге в начале этого руководства для регистрации приложения на портале Azure.

## <a name="for-ios-only-configure-url-schemes"></a>(Только для iOS) Настройка схем URL-адресов

На этом шаге вы зарегистрируете `CFBundleURLSchemes`, чтобы перенаправить пользователя обратно в приложение после входа в систему. Кстати, `LSApplicationQueriesSchemes` также позволяет вашему приложению использовать Microsoft Authenticator.

В Xcode откройте `Info.plist` как файл исходного кода и добавьте следующее в раздел `<dict>` Замените `[BUNDLE_ID]` на значение, которое было использовано на портале Azure, которое соответствует `com.microsoft.identitysample.MSALiOS` (при загрузке кода). Если вы создаете собственный проект, выберите свой проект в Xcode и откройте вкладку **Общие сведения**. Идентификатор пакета появляется в разделе **Удостоверение**.

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>(Только для macOS) Настройка песочницы приложения

1. Перейдите к параметрам проекта Xcode, а затем выберите **вкладку "Возможности"**  > **Песочница приложения**
2. Выберите флажок **Исходящие подключения (Клиент)** . 

## <a name="create-your-apps-ui"></a>Создание пользовательского интерфейса для приложения

Теперь создайте пользовательский интерфейс, в который включены кнопки для вызова Microsoft Graph API, для выхода и для текстового представления для просмотра некоторых результатов, добавив следующий код в класс `ViewController`:

### <a name="ios-ui"></a>Пользовательский интерфейс iOS

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
        callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
        // Add sign out button
        signOutButton = UIButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.setTitle("Sign Out", for: .normal)
        signOutButton.setTitleColor(.blue, for: .normal)
        signOutButton.setTitleColor(.gray, for: .disabled)
        signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

### <a name="macos-ui"></a>Пользовательский интерфейс macOS

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = NSButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.title = "Call Microsoft Graph API"
        callGraphButton.target = self
        callGraphButton.action = #selector(callGraphAPI(_:))
        callGraphButton.bezelStyle = .rounded
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        
        // Add sign out button
        signOutButton = NSButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.title = "Sign Out"
        signOutButton.target = self
        signOutButton.action = #selector(signOut(_:))
        signOutButton.bezelStyle = .texturedRounded
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = NSTextView()
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
        loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
        loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
    }
```

Затем, также внутри класса `ViewController` следует заменить метод `viewDidLoad()` на:

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }
    }
```

## <a name="use-msal"></a>Использование MSAL

### <a name="initialize-msal"></a>Инициализация MSAL

Добавьте в класс `ViewController` метод `initMSAL`.

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

Добавьте следующее после метода `initMSAL` в класс `ViewController`.

### <a name="ios-code"></a>код iOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(parentViewController: self)
    }
```

### <a name="macos-code"></a>код macOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
        self.webViewParameters?.webviewType = .wkWebView
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>(Только для iOS) Работа с обратным вызовом входа

Откройте файл `AppDelegate.swift` . Чтобы обработать обратный вызов после входа, добавьте `MSALPublicClientApplication.handleMSALResponse` в класс `appDelegate`, например:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Если вы используете Xcode 11**, то вместо этого в `SceneDelegate.swift` следует поместить обратный вызов MSAL.
Если UISceneDelegate и UIApplicationDelegate поддерживаются для совместимости с более старыми версиями iOS, то обратный вызов MSAL потребуется поместить в оба файла.

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

#### <a name="acquire-tokens"></a>Получение маркеров

Теперь мы можем реализовать логику обработки пользовательского интерфейса приложения и интерактивно получать маркеры через MSAL.

MSAL предоставляет два основных метода получения маркеров: `acquireTokenSilently()` и `acquireTokenInteractively()`. 

- `acquireTokenSilently()` пытается выполнить вход и получить маркеры без какого-либо взаимодействия с пользователем пока учетная запись существует.

- Метод `acquireTokenInteractively()` всегда отображает пользовательский интерфейс при попытке входа пользователя. Он может использовать файлы cookie сеанса в браузере или учетную запись в Microsoft Authenticator для реализации интерактивного единого входа.

Добавьте в класс `ViewController` приведенный ниже код.

```swift
    @objc func callGraphAPI(_ sender: AnyObject) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
    }

    func currentAccount() -> MSALAccount? {
        
        guard let applicationContext = self.applicationContext else { return nil }
        
        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        
        do {
            let cachedAccounts = try applicationContext.allAccounts()
            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }
        } catch let error as NSError {
            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }
        
        return nil
    }
```

#### <a name="get-a-token-interactively"></a>Интерактивное получение маркера

Приведенный ниже код первый раз возвращает маркер создав `MSALInteractiveTokenParameters` объект и вызвав `acquireToken`. Далее будет добавлен код, который делает следующее.

1. Создает `MSALInteractiveTokenParameters` с областями.
2. Вызывает `acquireToken()` с созданными параметрами.
3. Обрабатывает ошибки. Дополнительные сведения см. в [руководстве по обработке ошибок MSAL для iOS и macOS](msal-handling-exceptions.md).
4. Обрабатывает успешные выходные данные.

Добавьте в класс `ViewController` приведенный далее код.

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }
        
    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
        
    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in
            
        // #3
        if let error = error {
                
            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }
            
        guard let result = result else {
                
            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }
            
        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateSignOutButton(enabled: true)
        self.getContentWithToken()
    }
}    
```


#### <a name="get-a-token-silently"></a>Автоматическое получение маркера

Чтобы получить обновленный маркер без вывода сообщений, добавьте следующий код в класс `ViewController`. Он создает `MSALSilentTokenParameters` объект и вызывает `acquireTokenSilent()`:

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
                if (nsError.domain == MSALErrorDomain) {
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }
                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }
            
            guard let result = result else {
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Вызов API Microsoft Graph 

После получения маркера приложение может его использовать в заголовке HTTP, чтобы отправить авторизованный запрос к Microsoft Graph:

| ключ заголовка    | value                 |
| ------------- | --------------------- |
| Авторизация | Bearer \<маркер доступа> |

Добавьте в класс `ViewController` приведенный ниже код.

```swift
    func getContentWithToken() {        
        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)
        
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
               
        URLSession.shared.dataTask(with: request) { data, response, error in
               
        if let error = error {
            self.updateLogging(text: "Couldn't get graph result: \(error)")
            return
        }
               
        guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
               
        self.updateLogging(text: "Couldn't deserialize result JSON")
            return
        }
               
        self.updateLogging(text: "Result from Graph: \(result))")
        
        }.resume()
    }
```

Дополнительные сведения приведены на странице [Microsoft Graph](https://graph.microsoft.com).

### <a name="use-msal-for-sign-out"></a>Использование MSAL для выхода

Теперь добавим поддержку функции выхода.

> [!Important]
> Если функция выхода реализована с помощью MSAL, из приложения удаляется вся известная информация о пользователе, но сеанс на устройстве пользователя продолжает быть активным. Если пользователь пытается снова выполнить вход, может отобразиться интерфейс входа, в котором может не потребоваться повторно вводить учетные данные, так как сеанс на устройстве остается активным.

Чтобы добавить функцию выхода, добавьте следующий код в класс `ViewController`. Этот метод выполняет перебор всех учетных записей и удаляет их.

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.updateLogging(text: "")
            self.updateSignOutButton(enabled: false)
            self.accessToken = ""
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Включение кэширования маркеров

По умолчанию MSAL кэширует маркеры приложения в цепочку ключей iOS или macOS. 

Чтобы включить кэширования маркеров:
1. Убедитесь, что приложение подписано правильно
2. Перейдите к параметрам проекта Xcode, а затем выберите **Вкладка "Возможности"**  > **Включить общий доступ к цепочке ключей**.
3. Щелкните **+** и введите следующую запись **Групп цепочек ключей**. 3.a) для iOS введите `com.microsoft.adalcache` 3.б) для macOS введите `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Добавление вспомогательных методов
Чтобы выполнить пример, добавьте в класс `ViewController` следующие вспомогательные методы.

### <a name="ios-ui"></a>Пользовательский интерфейс iOS:

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
```

### <a name="macos-ui"></a>Пользовательский интерфейс macOS:

```swift
func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }
```



### <a name="multi-account-applications"></a>Приложения для нескольких учетных записей

Это приложение создано для сценария с одной учетной записью. MSAL также поддерживает сценарии с несколькими учетными записями, но это требует дополнительной работы с приложениями. Вам нужно будет создать пользовательский интерфейс, чтобы помочь пользователю выбрать нужную учетную запись для каждого действия, для которого требуются маркеры. Кроме того, приложение может реализовать эвристику для выбора нужной учетной записи с помощью метода `getAccounts()`.

## <a name="test-your-app"></a>Тестирование приложения

### <a name="run-locally"></a>Локальный запуск

Выполните сборку и развертывание приложения на тестовом устройстве или в симуляторе. При этом должен выполняться вход в приложение и получение маркеров для учетных записей Azure AD или личных учетных записей Майкрософт.

При первом входе пользователя в приложение от платформы удостоверений Майкрософт появится запрос на предоставление согласия на запрашиваемые разрешения.  Хотя большинство пользователей могут дать согласие, некоторые арендаторы Azure AD отключили согласие пользователей, что предусматривает получение согласия администраторов от имени всех пользователей. Для поддержки этого сценария обязательно зарегистрируйте области своего приложения на портале Azure.

После выполнения входа в приложении будут отображаться данные, возвращенные из конечной точки Microsoft Graph `/me`.

## <a name="get-help"></a>Получение справки

Если у вас возникли вопросы касательно этого руководства или платформы удостоверений Майкрософт, см. статью, посвященную [справке и поддержке](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options).

Помогите нам улучшить платформу Microsoft Identity. Поделитесь своим мнением, ответив на два вопроса.

> [!div class="nextstepaction"]
> [Опрос по платформе удостоверений Майкрософт](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
