---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: b7883de410a1fd281a154a792dd45132c08f0c03
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804992"
---
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Использование библиотеки проверки подлинности Майкрософт для получения маркера для API Microsoft Graph

Откройте файл `ViewController.swift` и замените код на следующий:

```swift
import UIKit
import MSAL

// A View Controller that will respond to the events of the Storyboard.
class ViewController: UIViewController, UITextFieldDelegate, URLSessionDelegate {

    // Replace Your_Application_Id_Here with the client ID you received in the portal. The below is for running the demo only.
    let kClientID = "Your_Application_Id_Here"

    // These settings you don't need to edit unless you wish to attempt deeper scenarios with the app.
    let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
    let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
    let kAuthority = "https://login.microsoftonline.com/common"

    var accessToken = String()
    var applicationContext : MSALPublicClientApplication?

    @IBOutlet weak var loggingText: UITextView!
    @IBOutlet weak var signoutButton: UIButton!

    /**
        Setup public client application in viewDidLoad
    */

    override func viewDidLoad() {

        super.viewDidLoad()

        do {

            /**
             Initialize a MSALPublicClientApplication with a given clientID and authority
             - clientId:            The clientID of your application, you should get this from the app portal.
             - authority:           A URL indicating a directory that MSAL can use to obtain tokens. In Azure AD
                                    it is of the form https://<instance/<tenant>, where <instance> is the
                                    directory host (e.g. https://login.microsoftonline.com) and <tenant> is a
                                    identifier within the directory itself (e.g. a domain associated to the
                                    tenant, such as contoso.onmicrosoft.com, or the GUID representing the
                                    TenantID property of the directory)
             - error                The error that occurred creating the application object, if any, if you're
                                    not interested in the specific error pass in nil.
             */

            guard let authorityURL = URL(string: kAuthority) else {
                self.loggingText.text = "Unable to create authority URL"
                return
            }

            let authority = try MSALAuthority(url: authorityURL)
            self.applicationContext = try MSALPublicClientApplication(clientId: kClientID, authority: authority)

        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }

    override func viewWillAppear(_ animated: Bool) {

        super.viewWillAppear(animated)
        signoutButton.isEnabled = !self.accessToken.isEmpty
    }

    /**
     This button will invoke the authorization flow.
    */

    @IBAction func callGraphButton(_ sender: UIButton) {

        if self.currentAccount() == nil {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            self.acquireTokenInteractively()
        } else {
            self.acquireTokenSilently()
        }
    }

    func acquireTokenInteractively() {

        guard let applicationContext = self.applicationContext else { return }

        applicationContext.acquireToken(forScopes: kScopes) { (result, error) in

            if let error = error {

                self.updateLogging(text: "Could not acquire token: \(error)")
                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken!
            self.updateLogging(text: "Access token is \(self.accessToken)")
            self.updateSignoutButton(enabled: true)
            self.getContentWithToken()
        }
    }

    func acquireTokenSilently() {

        guard let applicationContext = self.applicationContext else { return }

        /**
         Acquire a token for an existing account silently
         - forScopes:           Permissions you want included in the access token received
                                in the result in the completionBlock. Not all scopes are
                                guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
                                authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
                                flow completes, or encounters an error.
         */

        applicationContext.acquireTokenSilent(forScopes: kScopes, account: self.currentAccount()) { (result, error) in

            if let error = error {

                let nsError = error as NSError

                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.
                if (nsError.domain == MSALErrorDomain
                    && nsError.code == MSALErrorCode.interactionRequired.rawValue) {

                    DispatchQueue.main.async {
                        self.acquireTokenInteractively()
                    }

                } else {
                    self.updateLogging(text: "Could not acquire token silently: \(error)")
                }

                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken!
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignoutButton(enabled: true)
            self.getContentWithToken()
        }
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

<!--start-collapse-->
### <a name="more-information"></a>Дополнительные сведения

#### <a name="getting-a-user-token-interactively"></a>Интерактивное получение маркера пользователя

При вызове метода `acquireToken` откроется окно браузера, в котором пользователю предлагается выполнить вход. Приложениям обычно требуется, чтобы пользователь выполнял вход интерактивно при первом доступе к защищенному ресурсу или при сбое автоматической операции для получения маркера (например, срок действия пароля пользователя истек).

#### <a name="getting-a-user-token-silently"></a>Автоматическое получение маркера пользователя

Метод `acquireTokenSilent` обрабатывает получение и обновление маркера без участия пользователя. После первого выполнения метода `acquireToken` обычно используется метод `acquireTokenSilent`, чтобы получить маркеры для доступа к защищенным ресурсам для последующих вызовов (например, автоматическое выполнение запросов или обновлений маркеров).

В конечном счете в `acquireTokenSilent` произойдет сбой, например в случае выхода пользователя из системы или смены пароля на другом устройстве. Когда MSAL обнаруживает, что эту проблему можно решить, запросив интерактивное действие, возникает исключение `MSALErrorCode.interactionRequired`. Приложение может обработать это исключение двумя способами:

1. Вызвать `acquireToken` немедленно, в результате чего пользователю будет предложено выполнить вход. Этот шаблон обычно используется в интерактивных приложениях, где пользователю недоступно автономное содержимое. В примере приложения, созданного в ходе интерактивной настройки, используется следующий шаблон: вы увидите его в действии при первом запуске приложения. Так как приложение еще не использовалось, `applicationContext.allAccounts().first` будет содержать значение null и будет выдано исключение `MSALErrorCode.interactionRequired`. Код в примере обработает исключение, вызвав `acquireToken`, в результате чего пользователю будет предложено войти.

2. Приложения также могут визуально уведомить пользователя, что требуется интерактивный вход, чтобы пользователь мог выбрать подходящее время для входа или приложение могло повторить метод `acquireTokenSilent` ​​позднее. Обычно это применимо для тех случаев, когда пользователь может использовать другие функции приложения, на которые это не влияет, например, когда в приложении есть автономное содержимое. В этом случае пользователь может решить, когда он хочет войти, чтобы получить доступ к защищенному ресурсу или обновить устаревшие данные, или ваше приложение может решить повторить `acquireTokenSilent` при восстановлении сети ​​после временной недоступности.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Вызов API Microsoft Graph с помощью полученного маркера

Добавьте указанный ниже новый метод в файл `ViewController.swift`. Этот метод используется для выполнения запроса `GET` к API Microsoft Graph с использованием *Заголовка авторизации HTTP*:

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

<!--start-collapse-->
### <a name="making-a-rest-call-against-a-protected-api"></a>Вызов REST через защищенный API

В этом примере приложения метод `getContentWithToken()` выполняет HTTP-запрос `GET` к защищенному ресурсу, требующему маркер, а затем возвращает содержимое вызывающему объекту. Этот метод добавляет полученный маркер в *заголовок авторизации HTTP*. В этом примере ресурс — это конечная точка *me* API Microsoft Graph, которая отображает сведения о профиле пользователя.
<!--end-collapse-->

## <a name="set-up-sign-out"></a>Настройка выхода

Добавьте следующий метод в файл `ViewController.swift` для выхода пользователя:

```swift 
 @IBAction func signoutButton(_ sender: UIButton) {

        guard let applicationContext = self.applicationContext else { return }

        guard let account = self.currentAccount() else { return }

        do {

            /**
             Removes all tokens from the cache for this application for the provided account
             - account:    The account to remove from the cache
             */

            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signoutButton.isEnabled = false

        } catch let error as NSError {

            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }

}
```

<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>Дополнительные сведения о выходе

Метод `signoutButton` удаляет пользователя из кэша пользователей MSAL. Фактически это заставляет MSAL забыть о текущем пользователе, поэтому будущие запросы на получение маркеров будут успешными только в том случае, если будут выполняться автоматически.

Несмотря на то, что приложение в этом примере поддерживает одного пользователя, MSAL поддерживает сценарии, в которых несколько пользователей могут войти в систему одновременно, например для приложения электронной почты, в котором у пользователя есть несколько учетных записей.
<!--end-collapse-->

## <a name="register-the-callback"></a>Регистрация обратного вызова

После проверки подлинности пользователь перенаправляет пользователя обратно в приложение. Чтобы зарегистрировать обратный вызов, выполните указанные ниже действия:

1. Откройте `AppDelegate.swift` и импортируйте MSAL:

```swift
import MSAL
```

<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Добавьте следующий метод в класс <code>AppDelegate</code> для обработки обратных вызовов:
</li>
</ol>

```swift
// @brief Handles inbound URLs. Checks if the URL matches the redirect URI for a pending AppAuth
// authorization request and if so, will look for the code in the response.

func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    
    print("Received callback!")
    
    MSALPublicClientApplication.handleMSALResponse(url)
    
    return true
}
```
