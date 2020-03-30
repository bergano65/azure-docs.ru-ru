---
title: Отправка push-уведомлений в приложения Swift iOS, которые используют концентраторы уведомлений Azure Документы Майкрософт
description: Узнайте, как переплета уведомлений в приложения Swift iOS, которые используют концентраторы уведомлений Azure.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: a721c519c7a836e20455c6f1887bcfa7b52951f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336637"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>Учебник: Отправить push-уведомления в приложения Swift iOS с помощью API APPS концентраторов уведомлений

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

В этом уроке вы используете концентраторы уведомлений Azure для нажатия уведомлений в приложение для iOS на базе Swift с помощью [REST API.](/rest/api/notificationhubs/) Вы также создаете пустое приложение iOS, которое получает push-уведомления с помощью [службы apple Push Notification (APNs).](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)

Этот учебник приведет вас через следующие шаги:

> [!div class="checklist"]
> * Создание файла запроса на подписание сертификата.
> * Запросите приложение для push-уведомлений.
> * Создайте профиль подготовки приложения.
> * Создание центра уведомлений.
> * Наверстуйте концентратор уведомлений с информацией APN.
> * Подключите приложение iOS к концентратору уведомлений.
> * Проверьте решение.

Полный код для этого руководства можно найти на [GitHub](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest).

## <a name="prerequisites"></a>Предварительные требования

Для работы вам потребуется следующее:

- Чтобы пройти через [обзор центров уведомлений Azure,](notification-hubs-push-notification-overview.md) если вы не знакомы с службой.
- Чтобы узнать о [регистрации и установки](notification-hubs-push-notification-registration-management.md).
- Активная [учетная запись разработчика Apple](https://developer.apple.com).
- Mac под управлением Xcode вместе с действительным сертификатом разработчика, установленным в вашем Keychain.
- Физическое устройство iPhone вы можете запустить и отладить с, как вы не можете проверить push-уведомления с симулятором.
- Ваше физическое устройство iPhone зарегистрировано на [портале Apple](https://developer.apple.com) и связано с вашим сертификатом.
- [Подписка Azure,](https://portal.azure.com) в которой можно создавать и управлять ресурсами.

Даже если у вас нет предварительного опыта разработки iOS, вы должны быть в состоянии следовать по шагам для создания этого первого принципа примера. Тем не менее, вы получите пользу от знакомства со следующими понятиями:

- Создание приложений для iOS с Xcode и Swift.
- Настройка [концентратора уведомлений Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) для iOS.
- [Портал разработчиков Apple](https://developer.apple.com) и портал [Azure](https://portal.azure.com).

> [!NOTE]
> Концентратор уведомлений будет настроен только для использования режима проверки подлинности **песочницы.** Не следует использовать этот режим проверки подлинности для производственных нагрузок.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Подключите приложение iOS к концентратору уведомлений

В этом разделе вы создастприложение приложение iOS, которое будет подключаться к концентратору уведомлений.  

### <a name="create-an-ios-project"></a>Создание проекта iOS

1. В XCode создайте новый проект iOS и выберите шаблон **Single View Application** (Приложение с одним представлением).

1. При настройке вариантов нового проекта:

   1. Укажите **название продукта** (PushDemo) и **идентификатор организации** (),`com.<organization>`который вы использовали при установке **идентификатора комплекта** на портале разработчиков Apple.

   1. Выберите **команду,** для которого был настроен **идентификатор приложения.**

   1. Установите **язык** **swift**.

   1. Нажмите кнопку **Далее**.

1. Создайте новую папку под названием **SupportingFiles.**

1. Создайте новый файл p-list под названием **devsettings.plist** в **папке SupportingFiles.** Не забудьте добавить эту папку в файл **gitignore,** чтобы он не был совершен при работе с git repo. В производственном приложении вы, скорее всего, будете условно устанавливать эти секреты как часть автоматизированного процесса сборки. Такие настройки не описаны в этом пошаговом пошаговом листе.

1. Обновление **devsettings.plist,** чтобы включить следующие записи конфигурации, используя свои собственные значения из концентратора уведомлений, который вы приусловили:

   | Ключ                            | Тип                     | Значение                     |
   |--------------------------------| -------------------------| --------------------------|
   | уведомлениеHubKey             | Строка                   | \<hubKey>                  |
   | уведомлениеHubKeyName         | Строка                   | \<hubKeyName>              |
   | notificationHubName            | Строка                   | \<hubName>                 |
   | notificationHubNamespace       | Строка                   | \<hubNamespace>            |

   Необходимое значение можно найти, перенаправившись на ресурс концентратора уведомлений на портале Azure. В частности, значения **notificationHubName** и **notificationHubNamespace** находятся в правом верхнем углу резюме **Essentials** на странице **Обзор.**

   ![Уведомление концентраторы Основы резюме](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Вы также можете найти **значения notificationHubKeyName** и **notificationHubKey,** переориентируясь на **политики доступа** `DefaultFullSharedAccessSignature`и выбрав соответствующую политику **доступа,** например. После этого, копия из **основной строки подключения** значение, закрепленное `SharedAccessKeyName=` за `notificationHubKeyName` и значение, закрепленное `SharedAccessKey=` в `notificationHubKey`"Зените" для .

   Строка соединения должна быть в следующем формате:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Чтобы сохранить его `DefaultFullSharedAccessSignature` простым, укажите, чтобы вы могли использовать маркер для отправки уведомлений. На практике `DefaultListenSharedAccessSignature` это будет лучшим выбором для ситуаций, когда вы только хотите получать уведомления.

1. В рамках **проекта Navigator**выберите **название проекта,** а затем выберите **вкладку «Общая».**

1. Найти **идентификацию,** а затем установить значение `com.<organization>.PushDemo` **идентификатора bundle** так, чтобы оно соответствовало, которое является значением, используемым для **Идентификатора приложения** от предыдущего шага.

1. Найти **подписание & возможности,** а затем выбрать соответствующую **команду** для вашей **учетной записи разработчика Apple.** Значение **команды** должно соответствовать тому, под которым вы создали сертификаты и профили.

1. Xcode должен автоматически снизить соответствующее значение **профиля подготовки** на основе **идентификатора bundle.** Если вы не видите новое значение **профиля подготовки,** попробуйте обновить профили для **идентификации подписи,** выбрав**учетную запись** **Xcode** > **Preferences,** > а затем выберите кнопку **Скачать Руководство Профили** для загрузки профилей.

1. Тем не менее на вкладке **Подписание & возможностей,** нажмите кнопку **"Возможность"** и дважды нажмите на **Push Уведомления** из списка, чтобы обеспечить **Push Уведомления** включены.

1. Откройте файл **AppDelegate.swift** для реализации протокола **UNUserNotificationCenterDelegate** и добавьте следующий код в верхней части класса:

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {
        
        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        
        ...
    }
    ```

    Вы будете использовать эти члены позже. В частности, вы будете использовать **теги** члена как часть регистрации с помощью **пользовательского шаблона.** Для получения дополнительной информации о тегах, см [Теги для регистрации](notification-hubs-tags-segment-push-message.md) и [шаблон регистрации](notification-hubs-templates-cross-platform-push-messages.md).

1. В том же файле добавьте следующий код в функцию **didFinishLaunchingOptions:**

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    Этот код извлекает значения **настройки из devsettings.plist,** устанавливает класс **AppDelegate** в качестве делегата **UNUserNotificationCenter,** запрашивает авторизацию push-уведомлений, а затем вызывает **регистрДляудаленных уведомлений.**

    Чтобы сохранить его простым, код поддерживает *iOS 10 и только позже.* Вы можете добавить поддержку предыдущих версий ОС, условно используя соответствующие AA и подходы, как это обычно делается.

1. В том же файле добавьте следующие функции:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        let keyWindow = UIApplication.shared.windows.filter {$0.isKeyWindow}.first
        keyWindow?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    Код использует значения **installationId** и **pushChannel** для регистрации в концентраторе уведомлений. В этом случае вы используете **UIDevice.current.identifierForVendor** для обеспечения уникального значения для идентификации устройства, а затем форматирования **устройстваToken,** чтобы обеспечить желаемое значение **pushChannel.** Функция **showAlert** существует просто для отображения текста сообщения для демонстрационных целей.

1. Тем не менее в **файле AppDelegate.swift,** добавить **willPresent** и **didReceive** функции **UNUserNotificationCenterDelegate**. Эти функции отображают оповещение, когда они уведомлены о том, что приложение работает на переднем плане или фоновом режиме соответственно.

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. Добавьте печатные операторы в нижней части **didRegisterForRemoteNotificationsWithDeviceToken** функции для проверки того, что **installationId** и **pushChannel** присваиваются значения.

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. Создайте папки **«Модели,** **службы**и **утилиты»** для базовых компонентов, которые вы будете добавлять в проект позже.

1. Убедитесь, что проект строится и работает на физическом устройстве. Push-уведомления не могут быть проверены с помощью симулятора.

### <a name="create-models"></a>Создание моделей

На этом этапе вы создадите набор моделей для представления полезных нагрузок App API для [уведомлений кцентров](/rest/api/notificationhubs/) и хранения требуемых данных маркеров общей подписи доступа (SAS).

1. Добавьте новый файл Swift под названием **PushTemplate.swift** в папку **Модели.** Эта модель обеспечивает структуру, представляющую **BODY** отдельного шаблона как часть полезной нагрузки **DeviceInstallation.**

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Добавьте новый файл Swift под названием **DeviceInstallation.swift** в папку **Модели.** Этот файл определяет структуру, представляющую полезную нагрузку для создания или обновления **установки устройства.** Добавьте в него указанный ниже код.

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. Добавьте новый файл Swift под названием **TokenData.swift** в папку **Модели.** Эта модель будет использоваться для хранения токена SAS вместе с его истечением срока действия.

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>Создание маркера SAS

Концентраторы уведомлений используют ту же инфраструктуру безопасности, что и Azure Service Bus. Чтобы вызвать REST API, необходимо [запрограммировать генерацию токена SAS,](/rest/api/eventhub/generate-sas-token) который может быть использован в заголовке **авторизации** запроса.  

Полученный токен будет находиться в следующем формате:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Сам процесс включает в себя те же шесть ключевых шагов:  

1. Вычисление истечения срока действия в формате [времени UNIX Epoch,](https://en.wikipedia.org/wiki/Unix_time) что означает количество секунд, прошедшее с полуночи, скоординированное всеобщее время, 1 января 1970 года.
1. Форматирование **ResourceUrl,** представляющего ресурс, к который вы пытаетесь получить доступ, чтобы он закодирован на процент и ниже. **ResourceUrl** имеет форму `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Подготовка **StringToSign**, который отформатирован как `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`.
1. Вычисление и кодирование подписи Base64 **с** помощью хэша HMAC-SHA256 значения **StringToSign.** Значение хэша используется с **ключевой** частью **строки соединения** для соответствующего **правила авторизации.**
1. Форматирование Base64 закодировано **Подпись** так что процент закодирован.
1. Построение токена в ожидаемом формате с помощью значений **UrlEncodedSignature,** **ExpiryEpoch,** **KeyName**и **UrlEncodedResourceUrl.**

Ознакомиться с [документацией Azure Service Bus](../service-bus-messaging/service-bus-sas.md) можно для более тщательного обзора общей подписи доступа и того, как ее используют автобусы и концентраторы уведомлений Azure.

Для целей этого примера Swift вы собираетесь использовать библиотеку **CommonCrypto** с открытым исходным кодом Apple, чтобы помочь с хэшированием подписи. Как это библиотека C, он не доступен в Свифт из коробки. Вы можете сделать библиотеку доступной с помощью заголовок моста.

Чтобы добавить и настроить заголовок моста:

1. В Xcode **File**выберите > **файл New** > **File** > **Header File.** Назовите файл заголовка **BridgingHeader.h**.

1. Отспособите файл для импорта **CommonHMAC.h:**

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Обновление **параметров сборки** целевого показателя для ссылки на заголовок моста:

   1. Нажмите на проект **PushDemo** и прокрутите вниз в разделе **Swift Compiler.**

   1. Убедитесь, что параметр **«Установка объективной совместимости заголовка»** настроен на **Yes.**

   1. Введите `'<ProjectName>/BridgingHeader.h'` путь файла в опцию **«Целевая-C» в преодоление заголовка.** Это путь файла к нашему заголовок моста.

   Если вы не можете найти эти параметры, убедитесь, что у вас есть **все** представления выбраны, а не **основные** или **индивидуальные**.

   Есть много сторонних библиотек с открытым исходным кодом, которые могли бы сделать использование **CommonCrypto** немного проще. Однако обсуждение таких библиотек выходит за рамки данной статьи.

1. Добавьте новый файл Swift под названием **TokenUtility.swift** в папку **Утилиты** и добавьте следующий код:

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   Эта утилита инкапсулирует логику, ответственную за генерацию токена SAS.

   Как указывалось ранее, функция **getSasToken** организует шаги высокого уровня, необходимые для подготовки токена. Функция будет вызвана службой установки позже в этом учебнике.

   Две другие функции называются функцией **getSasToken:** **sha256HMac** для вычисления подписи и **urlEncodedString** для кодирования связанной строки URL. Функция **urlEncodedString** необходима, так как невозможно достичь требуемого вывода с помощью встроенной функции **добавленияPercentEncoding.**

   [SDK для хранения данных Azure](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) является прекрасным примером того, как подходить к этим операциям в Objective-C. Дополнительную информацию о токенах Azure Service Bus SAS можно найти в [документации Azure Service Bus.](../service-bus-messaging/service-bus-sas.md)

1. В **AppDelegate.swift**добавьте следующий код в функцию *didRegisterForRemoteNotificationsWithDeviceToken,* чтобы убедиться, что **TokenUtility.getSasToken** генерирует действительный токен
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    Не забудьте заменить значения заполнителя в **строке baseAddress** на свой собственный

### <a name="verify-the-sas-token"></a>Проверка токена SAS

Прежде чем реализовать службу установки в клиенте, убедитесь, что наше приложение правильно генерирует токен SAS, используя утилиту HTTP по выбору. Для целей этого учебника, наш инструмент выбора будет **Почтальон**.

Обратите внимание на значения **installationId** и **маркеров,** генерируемые приложением.

Выполните следующие действия, чтобы вызвать **API установок:**

1. В **Почтальон**, открыть новую вкладку.

1. Установите запрос на **GET** и укажите следующий адрес:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Наверсможно сяражку заголовков запросов следующим образом:

   | Ключ           | Значение            |
   | ------------- | ---------------- |
   | Content-Type  | приложение/json |
   | Авторизация | \<sasToken>       |
   | x-ms-version  | 2015-01          |

1. Выберите кнопку **Кода,** которая отображается в правом верхнем углу под кнопкой **«Сохранить».** Запрос должен выглядеть так же, как и следующий пример:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Нажмите кнопку **Send** (Отправить).

Регистрация указанной **установки** на данный момент не существует. Проверка должна привести к ответу "404 не найден", а не "401 несанкционированный" ответ. Этот результат должен подтвердить, что токен SAS был принят.

### <a name="implement-the-installation-service-class"></a>Реализация класса обслуживания установки

Далее вы будете реализовывать нашу основную обертку вокруг [API Установки REST.](/rest/api/notificationhubs/create-overwrite-installation)  

Добавьте новый файл Swift под названием **NotificationRegistrationService.swift** под папку **Службы,** а затем добавьте следующий код в этот файл:

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil
    private var tokenExpiryDate : Date? = nil
    
    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }
    
    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {
        
        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)
        
        if let tags = tags {
            deviceInstallation.tags = tags
        }
        
        if let templates = templates {
            deviceInstallation.templates = templates
        }
        
        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"
            
            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"
            
            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }
            
            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)
            
            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && 
                        (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            tokenExpiryDate == nil ||
            Date() >= tokenExpiryDate!) {
            
            self.tokenData = TokenUtility.getSasToken(
                forResourceUrl: getBaseAddress(),
                withKeyName: self.keyName,
                andKey: self.key)
            
            self.tokenExpiryDate = Date(timeIntervalSinceNow: -(5 * 60))
                .addingTimeInterval(TimeInterval(tokenData!.expiration))
        }

        return (tokenData?.token)!
    }
    
    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

Необходимые детали предоставляются в рамках инициализации. Теги и шаблоны по желанию передаются в функцию **регистра,** чтобы стать частью полезной нагрузки **установки устройства** JSON.  

Функция **регистра** вызывает другие частные функции для подготовки запроса. После получения ответа завершается выполнение и указывается, была ли регистрация успешной.  

Конечная точка запроса построена функцией **getBaseAddress.** Конструкция использует параметры концентратора уведомлений *и* *имя,* которые были предоставлены во время инициализации.  

Функция **getSasToken** проверяет, является ли сохраненный в настоящее время токен действительным. Если токен недействителен, функция вызывает **TokenUtility** для создания нового маркера, а затем хранит его перед возвращением значения.

Наконец, **encodeToJson** преобразует соответствующие объекты модели в JSON для использования в качестве части тела запроса.

### <a name="invoke-the-notification-hubs-rest-api"></a>Вызвать уведомления концентраторов REST API

Последним шагом является обновление **AppDelegate** использовать **NotificationRegistrationService** для регистрации в нашем **NotificationHub**.

1. Откройте **переменные AppDelegate.swift** и добавьте переменные уровня класса для хранения ссылки на **NoficiationRegistrationService** и общий **PushTemplate:**

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. В том же файле обновите **функцию didRegisterForRemoteNotificationsWithDeviceToToken,** чтобы инициализировать **NotificationRegistrationService** с необходимыми параметрами, а затем **вызовите** функцию регистра.

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Чтобы все было просто, вы собираетесь использовать несколько заявлений печати для обновления окна вывода с результатом работы **регистра.**

1. Теперь создайте и запустите приложение на физическом устройстве. Вы должны увидеть "Зарегистрировано" в окне вывода.

## <a name="test-the-solution"></a>Тестирование решения

Наше приложение на данном этапе зарегистрировано в **NotificationHub** и может получать push-уведомления. В Xcode остановите отладчик и закройте приложение, если оно в настоящее время работает. Затем убедитесь, что данные **установки устройства,** как ожидалось, и что наше приложение теперь может получать push-уведомления.  

### <a name="verify-the-device-installation"></a>Проверка установки устройства

Теперь вы можете сделать тот же запрос, что и ранее, используя **Postman** для [проверки токена SAS.](#verify-the-sas-token) Если предположить, что срок действия маркера SAS не истек, ответ должен теперь включать предоставленные вами сведения об установке, такие как шаблоны и теги.

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

Если срок действия предыдущего **токена SAS** истек, можно добавить **точку разрыва** в **строку 24** класса **TokenUtility,** чтобы получить новый **токен SAS** и обновить заголовок **авторизации** с этим новым значением.

### <a name="send-a-test-notification-azure-portal"></a>Отправить тест-уведомление (портал Azure)

Самый быстрый способ проверить, что теперь вы можете получать уведомления, это просматривать концентратор уведомлений на портале Azure:

1. На портале Azure просмотрите вкладку **«Обзор»** в концентраторе уведомлений.

1. Выберите **тест Отправить**, который выше **Резюме Essentials** в верхнем левом углу окна портала:

    ![Уведомление концентраторы Основы Резюме Тест Отправить кнопку](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Выберите **пользовательский шаблон** из списка **платформ.**

1. Введите **12345** для **выражения тегов.** Вы ранее указали этот тег в нашей установке.

1. Дополнительно отображают **сообщение** в полезной нагрузке JSON:

    ![Уведомление концентраторов Тест Отправить](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Выберите **Отправить**. Портал должен указать, было ли уведомление успешно отправлено на устройство:

    ![Тест концентраторов уведомлений Отправить результаты](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Предполагая, что приложение не работает на переднем плане, вы также должны увидеть уведомление в **Центре уведомлений** на устройстве. Нажатие уведомления должно открыть приложение и показать оповещение.

    ![Уведомление Получено Пример](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Отправить тест-уведомление (почтовый перевозчик)

Вы можете отправлять уведомления через [REST API](/rest/api/notificationhubs/) с помощью **Postman**, который может быть более удобным способом тестирования.

1. Откройте новую вкладку в **Postman**.

1. Установите запрос на **POST**и введите следующий адрес:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Наверсможно сяражку заголовков запросов следующим образом:

   | Ключ                            | Значение                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | приложение/json;charset'utf-8 |
   | Авторизация                  | \<sasToken>                     |
   | ServiceBusNotification-Формат  | шаблон                       |
   | Теги                           | 12345                        |

1. Направите запрос **BODY** на использование **RAW - JSON (application.json)** со следующей полезной нагрузкой JSON:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Выберите кнопку **Кода,** которая находится под кнопкой **«Сохранить»** в правом верхнем углу окна. Запрос должен выглядеть так же, как и следующий пример:

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. Нажмите кнопку **Send** (Отправить).

Вы должны получить **201 Созданный** код статуса успеха и получить уведомление на клиентском устройстве.

## <a name="next-steps"></a>Дальнейшие действия
Теперь у вас есть базовое приложение iOS Swift, подключенное к концентратору уведомлений через [REST API,](/rest/api/notificationhubs/) и вы можете отправлять и получать уведомления. Дополнительные сведения см. в следующих статьях:

- [Обзор концентрации уведомлений Azure](notification-hubs-push-notification-overview.md)
- [Использование интерфейса REST центров уведомлений](/rest/api/notificationhubs/)
- [Уведомления концентраторы SDK для бэк-энд ас-энда операций](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Уведомления концентраторы SDK на GitHub](https://github.com/Azure/azure-notificationhubs)
- [Зарегистрируйтесь с задней частью приложения](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Управление регистрацией](notification-hubs-push-notification-registration-management.md)
- [Работа с тегами](notification-hubs-tags-segment-push-message.md) 
- [Работа с пользовательскими шаблонами](notification-hubs-templates-cross-platform-push-messages.md)
- [Управление доступом к автобусу сервиса с общими подписями доступа](../service-bus-messaging/service-bus-sas.md)
- [Программно генерировать токены SAS](/rest/api/eventhub/generate-sas-token)
- [Безопасность Apple: распространенная криптография](https://developer.apple.com/security/)
- [Время эпохи UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [Hmac](https://en.wikipedia.org/wiki/HMAC)
