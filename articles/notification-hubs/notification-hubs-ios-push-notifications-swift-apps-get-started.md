---
title: Push-уведомления в приложениях Swift iOS, использовать центры уведомлений Azure | Документация Майкрософт
description: Узнайте, как отправлять Push-уведомления в приложениях Swift iOS, использовать центры уведомлений Azure.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.openlocfilehash: a4773ddd8114659118e89cfee57e73ddb39ff6b6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116680"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>Руководство по Push-уведомления в Swift iOS приложений, использующих API REST центров уведомлений

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

В этом руководстве можно использовать центры уведомлений Azure для Push-уведомлений в приложении на базе Swift iOS с помощью [REST API](/rest/api/notificationhubs/). Можно также создать пустое приложение iOS, получающее Push-уведомлений с помощью [Push-уведомлений Apple (APN)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

В этом руководстве описываются следующие действия:

> [!div class="checklist"]
> * Создайте файл запроса подписи сертификата.
> * Запрос приложения для Push-уведомлений.
> * Создайте профиль подготовки для приложения.
> * Создание центра уведомлений.
> * Настройка концентратора уведомлений с помощью APNs сведения.
> * Подключение приложения iOS в центр уведомлений.
> * Тестирование решения.

## <a name="prerequisites"></a>Технические условия

Для работы вам потребуется следующее:

- Чтобы пройти [обзоре центров уведомлений Azure](notification-hubs-push-notification-overview.md) Если вы не знакомы со службой.
- Дополнительные сведения о [регистрации и установки](notification-hubs-push-notification-registration-management.md).
- Активный [учетной записи разработчика Apple](https://developer.apple.com).
- Компьютер Mac с Xcode, а также разработчиков допустимый сертификат в цепочку ключей.
- Физический iPhone устройства можно запускать и отлаживать, так как невозможно тестировать Push-уведомлений с помощью симулятора.
- Физический iPhone устройство зарегистрировано в [портал Apple](https://developer.apple.com) и связанный с вашим сертификатом.
- [Подписки Azure](https://portal.azure.com) где можно создавать и администрировать ресурсы.

Даже если у вас нет опыта работы с разработкой приложений iOS, можно для выполнения этой процедуры действия для создания в этом примере первый и принципов. Тем не менее вас приятно удивит Знакомство со следующими принципами:

- Создание приложений iOS с Xcode и Swift.
- Настройка [центра уведомлений Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) для iOS.
- [Портал разработчиков Apple](https://developer.apple.com) и [портала Azure](https://portal.azure.com).

> [!NOTE]
> В центре уведомлений будут настроены для использования **"песочницы"** только режим проверки подлинности. Не следует использовать этот режим проверки подлинности для рабочих нагрузок.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Подключение приложения iOS в центр уведомлений

В этом разделе вам предстоит создать приложение iOS, которое будет подключаться к центру уведомлений.  

### <a name="create-an-ios-project"></a>Создание проекта iOS

1. В XCode создайте новый проект iOS и выберите шаблон **Single View Application** (Приложение с одним представлением).

1. При настройке параметров для нового проекта:

   1. Укажите **название продукта** (PushDemo) и **идентификатор организации** (`com.<organization>`), которые использовались при установке **идентификатор пакета** на портале разработчика Apple.

   1. Выберите **Team** , **идентификатор приложения** была настроена для.

   1. Задайте **языка** для **Swift**.

   1. Щелкните **Далее**.

1. Создайте новую папку с именем **SupportingFiles**.

1. Создайте новый список p-файл с именем **devsettings.plist** в **SupportingFiles** папки. Не забудьте добавить эту папку, чтобы ваши **gitignore** файл, так что он не зафиксирована, при работе с репозиторием git. В рабочем приложении вы бы скорее всего условной настройки эти секреты в рамках автоматизированного процесса сборки. Такие параметры не описаны в этом пошаговом руководстве.

1. Обновление **devsettings.plist** для включения следующие записи конфигурации, используя собственные значения из центра уведомлений, которое вы подготовили:

   | Ключ                            | type                     | Значение                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | <hubKey>                  |
   | notificationHubKeyName         | String                   | <hubKeyName>              |
   | notificationHubName            | String                   | <hubName>                 |
   | notificationHubNamespace       | String                   | <hubNamespace>            |

   Необходимые значения можно найти, перейдя по адресу ресурса центра уведомлений на портале Azure. В частности **notificationHubName** и **notificationHubNamespace** значения находятся в правом верхнем углу **Essentials** сводки в **Обзор** страницы.

   ![Сводка Essentials концентраторов уведомлений](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Также можно найти **notificationHubKeyName** и **notificationHubKey** значения, перейдя по адресу **политики доступа** и выбрав соответствующие  **Политика доступа**, такие как `DefaultFullSharedAccessSignature`. После этого скопируйте из **основной строки подключения** значения начинаются с префикса `SharedAccessKeyName=` для `notificationHubKeyName` и значения начинаются с префикса `SharedAccessKey=` для `notificationHubKey`.

   Строка подключения должна быть в следующем формате:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Проще говоря, укажите `DefaultFullSharedAccessSignature` , токен можно использовать для отправки уведомлений. На практике `DefaultListenSharedAccessSignature` будет лучшим выбором для ситуаций, где вы хотите получать уведомления.

1. В разделе **навигатор проекта**выберите **имя_проекта** , а затем выберите **Общие** вкладки.

1. Найти **удостоверений** и задайте **идентификатор пакета** значение, чтобы он соответствовал `com.<organization>.PushDemo`, что значение, используемое для **идентификатор приложения** из предыдущего шага.

1. Найти **подписывание**, а затем выберите соответствующий **Team** для вашей **учетной записи разработчика Apple**. **Team** значение должна совпадать с той, под которой вы создали сертификаты и профили.

1. Xcode должна автоматически извлечь вниз соответствующий **профиль подготовки** значение на основе **идентификатор пакета**. Если вы не видите новый **профиль подготовки** значений, обновите профили для **удостоверение подписывания** , выбрав **Xcode**  >  **Предпочтения** > **учетной записи** > **просмотреть сведения о**. Выберите **удостоверение подписывания**, а затем выберите **обновить** кнопки в правом нижнем загрузить профили.

1. Выберите **возможности** вкладку и убедитесь, что **Push-уведомления** включены.

1. Откройте ваш **AppDelegate.swift** файл для реализации **UNUserNotificationCenterDelegate** протокола и добавьте следующий код в начало класса:

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
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")

        ...
    }
    ```

    Эти члены будет использоваться в дальнейшем. В частности, вы используете **теги** и **genericTemplate** члены как часть регистрации. Дополнительные сведения о тегах см. в разделе [тегов для регистраций](notification-hubs-tags-segment-push-message.md) и [Регистрация шаблонов](notification-hubs-templates-cross-platform-push-messages.md).

1. В этом же файле добавьте следующий код, чтобы **didFinishLaunchingWithOptions** функции:

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

    Этот код извлекает значения параметров из **devsettings.plist**, задает **AppDelegate** классов как **UNUserNotificationCenter** делегировать, запросы авторизации для Push-уведомлений, а затем вызывает **registerForRemoteNotifications**.

    Проще говоря, этот код поддерживает *iOS 10 и более поздних версий*. Можно добавить поддержку для предыдущих версий операционной системы, условно используя соответствующие интерфейсы API и подходы, как это обычно делается.

1. В этом же файле добавьте следующие функции:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    Код использует **installationId** и **pushChannel** значения для регистрации в центре уведомлений. В этом случае вы используете **UIDevice.current.identifierForVendor** для предоставления уникальное значение для идентификации устройства, а затем форматирование **deviceToken** для обеспечения нужной  **pushChannel** значение. **ShowAlert** существует функция просто для того, чтобы отобразить текст сообщения для демонстрационных целей.

1. По-прежнему в **AppDelegate.swift** добавьте **willPresent** и **didReceive** функции **UNUserNotificationCenterDelegate**. Эти функции отображать оповещение, когда они информирующее пользователя, приложение выполняется в основном или фоновом режиме, соответственно.

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

1. Добавьте в конец выражения print **didRegisterForRemoteNotificationsWithDeviceToken** функции, чтобы убедиться, что **installationId** и **pushChannel** выполняется присвоенные значения.

1. Создание **моделей**, **служб**, и **служебные программы** папки для основных компонентов необходимо добавить в проект позже.

1. Убедитесь, что проект выполняется сборка и запуск на физическом устройстве. Push-уведомлений нельзя протестировать с помощью симулятора.

### <a name="create-models"></a>Создание моделей

На этом шаге вы создадите набор моделей для представления [API REST концентраторов уведомлений](/rest/api/notificationhubs/) полезных данных и для хранения общего доступа URL-адреса (SAS) маркеров данных.

1. Добавить новый Swift файл с именем **PushTemplate.swift** для **моделей** папки. Эта модель предоставляет объект, представляющий структуру **текст** отдельного шаблона как часть **DeviceInstallation** полезных данных.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Добавить новый Swift файл с именем **DeviceInstallation.swift** для **моделей** папки. Этот файл определяет структуру, представляющий полезные данные для создания или обновления **установки устройств**. Добавьте в него указанный ниже код.

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

1. Добавить новый Swift файл с именем **TokenData.swift** для **моделей** папки. Эта модель будет использоваться для хранения маркер SAS, а также срок его действия.

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

### <a name="generate-a-sas-token"></a>Создать маркер SAS

Центры уведомлений используют одну инфраструктуру безопасности как служебной шины Azure. Чтобы вызвать REST API, вам потребуется [программным способом создать маркер SAS](/rest/api/eventhub/generate-sas-token) , используемый в **авторизации** заголовок запроса.  

Результирующий маркер будет иметь следующий формат:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Сам процесс включает в себя те же действия шесть ключей:  

1. Вычисления истечения срока действия в [виде времени эпохи UNIX](https://en.wikipedia.org/wiki/Unix_time) формат, что означает количество секунд, истекших после полуночи UTC, 1 января 1970 года.
1. Форматирование **ResourceUrl** , представляющий ресурс, который вы пытаетесь открыть, чтобы его процентной кодировке и в нижнем регистре. **ResourceUrl** имеет форму `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Подготовка **StringToSign**, который представляется в формате `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`.
1. Вычислений и кодировку Base64 **подпись** с помощью хэш HMAC-SHA256 **StringToSign** значение. Хэш-значение используется с **ключ** частью **строку подключения** для соответствующих **правило авторизации**.
1. Форматирование в кодировке Base64 **подпись** это знаком процента.
1. Создав маркер в требуемом формате, с помощью **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**, и **UrlEncodedResourceUrl** значения.

См. в разделе [документации по служебной шине](../service-bus-messaging/service-bus-sas.md) полно Обзор подпись общего доступа и служебной шины Azure и концентраторов уведомлений его использовании.

В целях этого примера Swift, вы собираетесь использовать с открытым исходным кодом Apple **CommonCrypto** библиотеки для хэширования, подписи. Как это библиотеки, он недоступен в Swift без дополнительной настройки. Вы можете предоставить библиотеки с помощью промежуточный заголовок.

Чтобы добавить и настроить заголовок.

1. В Xcode, выберите **файл** > **New** > **файл** > **файл заголовка**. Назовите файл заголовка **BridgingHeader.h**.

1. Измените файл для импорта **CommonHMAC.h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Обновить целевой объект **параметры построения** для ссылки на заголовок:

   1. Откройте **стандартных параметров** вкладку и прокрутите вниз до **компилятор Swift** раздел.

   1. Убедитесь, что **установить заголовок Objective-C совместимости** параметру присваивается **Да**.

   1. Введите путь к файлу `'<ProjectName>/BridgingHeader.h'` в **мост заголовка Objective-C** параметр. Это путь к нашей промежуточный заголовок.

   Если не удается найти эти параметры, убедитесь, что у вас есть **все** выбрано представление вместо **основные** или **настроенная**.

   Есть много программы-оболочки для независимых производителей открытым кодом библиотеки, которые могут сделать с помощью **CommonCrypto** более удобным. Тем не менее обсуждение таких библиотек выходит за рамки данной статьи.

1. Добавьте новый файл Swift **TokenUtility.swift** в **служебные программы** папку и добавьте следующий код:

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

   Эта служебная программа содержит логику, ответственными за генерирование маркер SAS.

   Как описано ранее, **getSasToken** функция организует высокоуровневые шаги, необходимые для подготовки маркер. Функция будет вызываться службой установки далее в этом руководстве.

   Две другие функции вызываются **getSasToken** функция: **sha256HMac** для вычисления подписи и **urlEncodedString** для кодирования связан URL-адрес Строка. **UrlEncodedString** функции является обязательным, так как оно не невозможно обеспечить требуемые выходные данные с помощью встроенной **addingPercentEncoding** функции.

   [Пакет SDK для iOS хранилища Azure](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) является отличным примером как подходить к эти операции в Objective-C. Дополнительные сведения о токенах SAS служебной шины Azure можно найти в [документации по служебной шине](../service-bus-messaging/service-bus-sas.md).

### <a name="verify-the-sas-token"></a>Проверки маркера SAS

Прежде чем реализовать службу установки в клиенте, проверьте, что наше приложение правильно создает маркер SAS с помощью вашей программы HTTP, по выбору. Для целей данного учебника, будет наше средство выбора **Postman**.

Использовать соответствующим образом помещено инструкция print или точки останова обратите **installationId** и **маркера** значений, создаваемый приложением.

Выполните следующие действия для вызова **установок** API:

1. В **Postman**, откройте новую вкладку.

1. Присвоить запросу **получить** и указать следующий адрес:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Настройте заголовки запроса следующим образом:

   | Ключ           | Значение            |
   | ------------- | ---------------- |
   | Content-Type  | приложение/json |
   | Авторизация | <sasToken>       |
   | x-ms-version  | 2015-01          |

1. Выберите **кода** кнопку, которая отображается в правом верхнем углу под **Сохранить** кнопки. Запрос должен выглядеть аналогично приведенному ниже:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Выберите **отправки** кнопки.

Регистрация не существует для указанного **installationId** на этом этапе. Результатом проверки должен быть в ответ «404 не найдено», а не ответа «401 — не санкционировано». Этот результат необходимо убедиться, что маркер SAS были приняты.

### <a name="implement-the-installation-service-class"></a>Реализовать класс службы установки

Затем вы реализуете наши основные оболочку вокруг [установок REST API](/rest/api/notificationhubs/create-overwrite-installation).  

Добавьте Swift файл с именем **NotificationRegistrationService.swift** в разделе **служб** папку, а затем добавьте следующий код в этот файл:

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
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }

    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }

    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
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

Все необходимые подробности в процессе инициализации. Теги и шаблоны при необходимости передаются в **зарегистрировать** функции являются частью **установки устройств** полезные данные JSON.  

**Зарегистрировать** функция вызывает другие закрытые функции, чтобы подготовить запрос. После получения ответа, завершения называется и указывает, успешно ли регистрация.  

Конечная точка запроса создается с помощью **getBaseAddress** функции. Конструкции используются параметры концентратора уведомлений *пространства имен* и *имя* , предоставленные во время инициализации.  

**GetSasToken** функция проверяет, является ли допустимым данный момент хранимый маркер. Если маркер является недопустимым, функция вызывает **TokenUtility** для создания нового маркера и затем сохраняет его, прежде чем возвращать значение.

Наконец **encodeToJson** преобразует объекты соответствующей модели в JSON для использования в качестве части текста запроса.

### <a name="invoke-the-notification-hubs-rest-api"></a>Вызвать REST API для центров уведомлений

Последним шагом обновление **AppDelegate** использовать **NotificationRegistrationService** для регистрации в нашей **NotificationHub**.

1. Откройте **AppDelegate.swift** и добавьте переменную уровня класса для хранения ссылки на **NotificationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. В этом же файле обновите **didRegisterForRemoteNotificationsWithDeviceToken** функцию для инициализации **NotificationRegistrationService** с необходимые параметры, а затем вызовите метод **зарегистрировать** функции.

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

    Проще говоря, вы собираетесь использовать несколько выражения print для обновления в окне вывода с результатом **зарегистрировать** операции.

1. Теперь сборка и запуск приложения на физическом устройстве. Вы должны увидеть «Зарегистрировано» в окне вывода.

## <a name="test-the-solution"></a>Тестирование решения

Наше приложение на этом этапе регистрируется с **NotificationHub** и может получать Push-уведомлений. В Xcode остановите отладчик и закрыть приложение, если он выполняется в данный момент. Затем убедитесь, что **установки устройств** сведения будут должным образом и что наше приложение теперь могут получать Push-уведомлений.  

### <a name="verify-the-device-installation"></a>Проверка установки устройства

Теперь можно сделать тот же запрос, как это делалось ранее с помощью **Postman** для [проверки маркера SAS](#verify-the-sas-token). При условии, что маркер SAS еще не истек, ответ теперь должен содержать сведения об установке, которые вы указали, такие как шаблоны и теги.

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

### <a name="send-a-test-notification-azure-portal"></a>Отправить тестовое уведомление (портал Azure)

Чтобы перейти в центр уведомлений на портале Azure — самый быстрый способ проверить, что вы теперь можете получать уведомления:

1. На портале Azure перейдите к **Обзор** вкладку в концентраторе уведомлений.

1. Выберите **Тестовая отправка**, который находится над **Essentials** сводки в левом верхнем углу окна портала:

    ![Кнопка отправки сводки теста Essentials концентраторов уведомлений](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Выберите **пользовательский шаблон** из **платформ** списка.

1. Введите **12345** для **отправить в выражение тега**. Этот тег уже была задана в нашей установки.

1. При необходимости изменить **сообщение** в полезных данных JSON:

    ![Тестовая отправка концентраторов уведомлений](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Нажмите кнопку **Отправить**. Портал должно быть указано ли уведомление было успешно отправлено на устройство:

    ![Проверка концентраторов уведомления Отправка результатов](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Предположим, что приложение не выполняется на переднем плане, вы также увидите уведомление в **центр уведомлений** на вашем устройстве. При нажатии уведомления следует открыть приложение и отображать сообщение.

    ![Получено уведомление о примере](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Отправить тестовое уведомление (Mail несущей)

Вы можете отправлять уведомления с помощью [REST API](/rest/api/notificationhubs/) с помощью **Postman**, который может быть более удобным способом для тестирования.

1. Откройте новую вкладку в **Postman**.

1. Присвоить запросу **POST**и введите следующий адрес:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Настройте заголовки запроса следующим образом:

   | Ключ                            | Значение                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Авторизация                  | <sasToken>                     |
   | Формат ServiceBusNotification  | шаблон                       |
   | Tags                           | 12345                        |

1. Настройте запрос **текст** использовать **RAW - JSON (application.json)** с следующие полезные данные JSON:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Выберите **кода** кнопку, которая находится в стадии **Сохранить** кнопку в правом верхнем углу окна. Запрос должен выглядеть аналогично приведенному ниже:

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

1. Выберите **отправки** кнопки.

Следует получить код состояния успеха и получать уведомления на клиентском устройстве.

## <a name="next-steps"></a>Дальнейшие действия
Теперь у вас есть основные iOS Swift приложения, подключенного к концентратора уведомлений с помощью [REST API](/rest/api/notificationhubs/) и может отправлять и получать уведомления. Дополнительные сведения см. в следующих статьях:

- [Общие сведения о центрах уведомлений Azure](notification-hubs-push-notification-overview.md)
- [Использование интерфейса REST центров уведомлений](/rest/api/notificationhubs/)
- [Пакет SDK для центров уведомлений для операций серверной части](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Концентраторы уведомлений SDK на сайте GitHub](https://github.com/Azure/azure-notificationhubs)
- [Регистрация с помощью серверной части приложения](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Управление регистрацией](notification-hubs-push-notification-registration-management.md)
- [Работа с тегами](notification-hubs-tags-segment-push-message.md) 
- [Работа с пользовательскими шаблонами](notification-hubs-templates-cross-platform-push-messages.md)
- [Управление доступом служебной шины с подписями общего доступа](../service-bus-messaging/service-bus-sas.md)
- [Программное создание маркеров SAS](/rest/api/eventhub/generate-sas-token)
- [Apple безопасности: общие crypto](https://developer.apple.com/security/)
- [Время начала эпохи UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
