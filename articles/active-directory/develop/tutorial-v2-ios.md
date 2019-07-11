---
title: Начало работы с iOS и платформой удостоверений Майкрософт | Azure
description: Как приложения iOS (Swift) могут вызывать API, которому необходимы маркеры доступа, используя платформу удостоверений Майкрософт
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7d68f6f7079872b81b750ba71997117aaa27d33
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550575"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Вход пользователей и вызов Microsoft Graph из приложения iOS

Из этого руководства вы узнаете, как создать приложение iOS и интегрировать его с платформой удостоверений Майкрософт. В частности, приложение авторизует пользователя, получит маркер доступа для вызова API Microsoft Graph и выполнит базовый запрос к API Microsoft Graph.  

Когда вы завершите работу с этим руководством, ваше приложение сможет принимать операции входа с помощью личных учетных записей Майкрософт (включая outlook.com, live.com и другие), а также рабочих или учебных учетных записей из любой компании или организации, в которых используется Azure Active Directory.

## <a name="how-this-guide-works"></a>Принцип работы с руководством

![Схема работы примера приложения, создаваемого в этом кратком руководстве](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

В этом примере приложение авторизует пользователя и получает данные от его имени.  Доступ к этим данным будет осуществляться через защищенный API (в данном случае API Microsoft Graph), который требует авторизации и также защищен платформой удостоверений Майкрософт.

В частности:

* Приложение авторизует пользователя через браузер или Microsoft Authenticator.
* Пользователь принимает разрешения, которые запрашивает приложение. 
* Приложение выдает маркер доступа для API Microsoft Graph.
* Этот маркер доступа будет включен в HTTP-запрос к веб-API.
* Затем обрабатывается ответ Microsoft Graph.

В этом примере используется библиотека проверки подлинности Майкрософт (MSAL) для реализации проверки подлинности. MSAL автоматически обновляет маркеры, обеспечивает единый вход для других приложений на устройстве, а также управляет учетными записями.

## <a name="prerequisites"></a>Предварительные требования

- Для создания примера в этом руководстве используется XCode версии 10.x. Скачать XCode можно с [сайта iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode Download URL").
- Библиотека проверки подлинности Майкрософт ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Вы можете использовать диспетчер зависимостей или добавить ее вручную. [Дополнительные сведения](#add-msal) см. в приведенном ниже разделе. 

## <a name="set-up-your-project"></a>Настройка проекта

В рамках этого руководства будет создан проект. Если вместо этого вы хотите скачать готовое руководство, [скачайте код](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>Создание нового проекта

1. Запустите Xcode и щелкните **Create a new Xcode project** (Создать новый проект Xcode).
2. Выберите **iOS > Single view Application** (iOS > Приложение с одним представлением) и нажмите кнопку **Next** (Далее).
3. Введите название продукта и нажмите кнопку **Next** (Далее).
4. Выберите папку для создания приложения и нажмите кнопку *Создать*.

## <a name="register-your-application"></a>Регистрация приложения 

Приложение можно зарегистрировать одним из двух способов, которые описаны в следующих двух разделах.

### <a name="register-your-app"></a>Регистрация приложения

1. Перейдите на [портал Azure](https://aka.ms/MobileAppReg) и выберите `New registration` (Новая регистрация). 
2. Введите **имя** приложения и выберите `Register` (Зарегистрировать). **Не устанавливайте URI перенаправления на этом этапе**. 
3. В разделе `Manage` (Управление) перейдите к `Authentication` (Проверка подлинности) > `Add a platform` (Добавить платформу) > `iOS`.
    - Введите идентификатор пакета проекта. Если вы скачали код, это будет `com.microsoft.identitysample.MSALiOS`.
4. Щелкните `Configure` (Настроить) и сохраните `MSAL Configuration` (конфигурацию MSAL) для использования в дальнейшем. 

## <a name="add-msal"></a>Добавление MSAL

### <a name="get-msal"></a>Получение MSAL

#### <a name="cocoapods"></a>CocoaPods

Вы можете использовать инструмент [CocoaPods](https://cocoapods.org/), чтобы установить библиотеку `MSAL`, добавив ее к `Podfile` в разделе целевого объекта:

```
use_frameworks!

target '<your-target-here>' do
   pod 'MSAL', '~> 0.4.0'
end
```

#### <a name="carthage"></a>Carthage

Вы можете использовать [Carthage](https://github.com/Carthage/Carthage), чтобы установить библиотеку `MSAL`, добавив ее в `Cartfile`: 

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

#### <a name="manually"></a>Вручную

Установить библиотеку также можно с помощью подмодуля Git. Кроме того, вы можете извлечь последний выпуск и использовать его в качестве платформы в своем приложении.

### <a name="add-your-app-registration"></a>Добавление регистрации приложения

Затем добавьте регистрацию приложения в код. Добавьте ***идентификатор клиента или приложения*** в `ViewController.swift`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

### <a name="configure-url-schemes"></a>Настройка схем URL-адресов

Зарегистрируйте `CFBundleURLSchemes`, чтобы разрешить обратный вызов. Таким образом пользователь будет перенаправляться обратно в приложение после входа.

`LSApplicationQueriesSchemes` позволяет использовать приложение для работы с приложением Microsoft Authenticator, если оно доступно. 

Чтобы сделать это, откройте `Info.plist` в качестве исходного кода и добавьте следующий код, заменив ***BUNDLE_ID*** на то, что вы настроили на портале Azure.

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
    <string>msauth</string>
    <string>msauthv2</string>
</array>
```

### <a name="import-msal"></a>Импорт MSAL

Импортируйте платформу MSAL в файлы `ViewController.swift` и `AppDelegate.swift`:

```swift
import MSAL
```

## <a name="create-your-apps-ui"></a>Создание пользовательского интерфейса для приложения

Для работы с этим руководством необходимо создать:

- кнопку вызова API Graph;
- кнопку выхода;
- текстовое представления для ведения журналов.

В `ViewController.swift` определите свойства и `initUI()` следующим образом:

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

Затем добавьте ViewController.swift к `viewDidLoad()`:

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }
```

## <a name="use-msal"></a>Использование MSAL

### <a name="initialize-msal"></a>Инициализация MSAL

Сначала необходимо создать `MSALPublicClientApplication` с одним экземпляром `MSALPublicClientConfiguration` для приложения:

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.loggingText.text = "Unable to create authority URL"
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
    }
```

### <a name="handle-the-callback"></a>Обработка обратного вызова

Чтобы обработать обратный вызов после входа, добавьте `MSALPublicClientApplication.handleMSALResponse` в `appDelegate`:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Получение маркеров

Теперь мы можем реализовать логику обработки пользовательского интерфейса приложения и интерактивно получать маркеры через MSAL. 

MSAL предоставляет два основных метода получения маркеров: `acquireTokenSilently` и `acquireTokenInteractively`.  

`acquireTokenSilently()` пытается выполнить вход и получить маркеры без какого-либо взаимодействия с пользователем при условии наличия учетной записи.

`acquireTokenInteractively` всегда будет отображать пользовательский интерфейс при попытке входа и получения маркеров. Однако для обеспечения интерактивного единого входа могут использоваться файлы cookie сеанса в браузере или учетная запись в Microsoft Authenticator. 

```swift
    @objc func callGraphAPI(_ sender: UIButton) {
        
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

Чтобы получить маркер в первый раз, необходимо создать `MSALInteractiveTokenParameters` и вызвать `acquireToken`.

1. Создайте `MSALInteractiveTokenParameters` с областями.
2. Вызовите `acquireToken` с созданными параметрами.
3. Обработайте ошибку соответствующим образом. Дополнительные сведения см. в [руководстве по обработке ошибок iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Обработайте успешные выходные данные. 

```swift
    func acquireTokenInteractively() {
   
        guard let applicationContext = self.applicationContext else { return }
     // #1    
        let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
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

Чтобы автоматически получить обновленный маркер, необходимо создать `MSALSilentTokenParameters` и вызвать `acquireTokenSilent`:

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

После получения маркера через `self.accessToken` приложение может использовать это значение в заголовке HTTP, чтобы отправить авторизованный запрос к Microsoft Graph:

| ключ заголовка    | value                 |
| ------------- | --------------------- |
| Авторизация | Bearer \<маркер доступа> |

Добавьте следующую строку в файл `ViewController.swift`:

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

Дополнительные сведения об [API Microsoft Graph](https://graph.microsoft.com).

### <a name="use-msal-for-sign-out"></a>Использование MSAL для выхода

Теперь мы добавим поддержку для выхода в приложении. 

Следует отметить, что в случае выхода с помощью MSAL из этого приложения удаляется вся известная информация о пользователе, но у пользователя все еще будет активный сеанс на его устройстве. Если пользователь пытается войти снова, он может увидеть взаимодействие, но повторный ввод учетных данных может не потребоваться из-за активного сеанса устройства. 

Чтобы добавить выход, скопируйте следующий метод в `ViewController.swift`:

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signOutButton.isEnabled = false
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Включение кэширования маркеров

По умолчанию MSAL кэширует маркеры приложения в цепочку ключей iOS. 

Чтобы включить кэширование маркеров, перейдите к параметрам проекта Xcode, выберите вкладку `Capabilities tab` (Возможности) > `Enable Keychain Sharing` (Включить обмен цепочкой ключей), щелкните `Plus` (знак плюса) и введите **com.microsoft.adalcache**.

### <a name="add-helper-methods"></a>Добавление вспомогательных методов

Добавьте эти вспомогательные методы, чтобы выполнить пример:

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

### <a name="multi-account-applications"></a>Приложения для нескольких учетных записей

Это приложение создано для сценария с одной учетной записью. MSAL также поддерживает сценарии с несколькими учетными записями, но это требует дополнительной работы с приложениями. Вам нужно будет создать пользовательский интерфейс, чтобы помочь пользователю выбрать нужную учетную запись для каждого действия, для которого требуются маркеры. Кроме того, приложение может реализовать эвристику для выбора нужной учетной записи с помощью метода `getAllAccounts(...)`.

## <a name="test-your-app"></a>Тестирование приложения

### <a name="run-locally"></a>Локальный запуск

Если вы следовали приведенному выше коду, попробуйте создать и развернуть приложение на устройстве для тестирования или эмуляторе. У вас должна быть возможность входить и получать маркеры для учетных записей Azure AD или личных учетных записей Майкрософт. После входа пользователя это приложение будет отображать данные, возвращенные из конечной точки `/me` Microsoft Graph. 

Если у вас возникли какие-либо проблемы, вы можете сообщить о проблеме в этом документе или в библиотеке MSAL и связаться с нами. 

### <a name="consent-to-your-app"></a>Предоставление согласия для приложения

При первом входе любого пользователя в приложение от платформы удостоверений Майкрософт появится запрос на предоставление согласия на запрашиваемые разрешения.  Хотя большинство пользователей могут дать согласие, некоторые арендаторы Azure AD отключили согласие пользователей, требуя, чтобы администраторы давали согласие от имени всех пользователей.  Для поддержки этого сценария обязательно зарегистрируйте области своего приложения на портале Azure.

## <a name="help-and-support"></a>Справка и поддержка

Возникли какие-либо проблемы с этим руководством или с платформой удостоверений Майкрософт? Ознакомьтесь со статьей [Возможности получения поддержки и справки для разработчиков](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options).

