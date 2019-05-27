---
title: Push-уведомления в приложения Swift iOS с помощью центров уведомлений Azure | Документация Майкрософт
description: Узнайте, как отправлять Push-уведомления приложения Swift iOS, с помощью центров уведомлений Azure.
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
ms.openlocfilehash: 64a05c6cbd412953ae10b31efc1d141c9cefd062
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65994771"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-using-the-notification-hubs-rest-api"></a>Руководство по Push-уведомления в приложения Swift iOS с помощью API REST центров уведомлений

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

В этом руководстве, использовать центры уведомлений Azure для Push-уведомлений в приложение на базе Swift iOS с помощью [REST API](/rest/api/notificationhubs/). Вы создадите пустое приложение iOS, получающее push-уведомления с помощью [Службы push-уведомлений Apple (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

В этом руководстве вы выполните следующие задачи:

> [!div class="checklist"]
> * создание файла запроса на подпись сертификата;
> * регистрация приложения для работы с push-уведомлениями;
> * Создание профиля подготовки для приложения
> * Создание концентратора уведомлений
> * Настройка концентратора уведомлений с помощью APNS сведения
> * подключение приложения iOS к центрам уведомлений;
> * Тестирование решения

## <a name="prerequisites"></a>Технические условия
Для работы вам потребуется следующее:

- Пройдите [обзоре центров уведомлений Azure](notification-hubs-push-notification-overview.md) Если вы не знакомы со службой. 
- Дополнительные сведения об установке и регистрации: [Управление регистрацией](notification-hubs-push-notification-registration-management.md)
- Активный [учетной записи разработчика Apple](https://developer.apple.com) 
- Компьютер Mac с Xcode, а также разработчиков допустимый сертификат в цепочку ключей
- Физический iPhone устройства можно запустить и отладить с (невозможно тестировать Push-уведомления с помощью симулятора)
- Физический iPhone устройство зарегистрировано в [портал Apple](https://developer.apple.com) и связанный с вашим сертификатом
- [Подписки Azure](https://portal.azure.com) где можно создавать и администрировать ресурсы

Оно должно быть возможно для выполнения этой процедуры для создания в этом примере первый и принципов, не имея опыта работы. Однако знание следующие основные понятия может оказаться полезной:

- Создание приложений iOS с Xcode и Swift
- Настройка [центра уведомлений Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) для iOS
- Знакомство с [портал разработчиков Apple](https://developer.apple.com) и [портала Azure](https://portal.azure.com)

> [!NOTE]
> В центре уведомлений будут настроены для использования *"песочницы"* только режим проверки подлинности. Не следует использовать этот режим проверки подлинности для рабочих нагрузок.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Подключение приложения iOS к центрам уведомлений
В этом разделе вам предстоит создать приложение iOS, которое будет подключаться к центру уведомлений.  

### <a name="create-an-ios-project"></a>Создание проекта iOS
1. В **Xcode**, создайте новый проект iOS и выберите **приложение одного представления** шаблона.
2. При настройке параметров для нового проекта, выполните следующие действия.
    1. Используйте тот же **название продукта** (то есть **PushDemo**) и **идентификатор организации** (то есть `com.<organization>`), используемую при **пакета Идентификатор** было задано в **портал разработчиков Apple**. 
    2. Выберите **Team** , **идентификатор приложения** была настроена для.
    3. Задайте **языка** для **Swift**.
    4. Щелкните **Далее**
3. Создайте новую папку с именем **SupportingFiles**.
4. Создайте новый **plist** файл с именем **devsettings.plist** под **SupportingFiles** папки. Не забудьте добавить эту папку, чтобы ваши **gitignore** файл, так что он не зафиксирована, при работе с **репозитория git**. В рабочем приложении вы бы скорее всего условной настройки эти секреты в рамках автоматизированного процесса сборки. Тем не менее он не рассматривается как часть этого пошагового руководства.
5. Обновление **devsettings.plist** включать следующие записи конфигурации (используя свои значения из **концентратора уведомлений** вам подготовить):

   | Ключ                            | type                     | Value                     |               
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey\>                |
   | notificationHubKeyName         | String                   | \<hubKeyName\>            |
   | notificationHubName            | String                   | \<hubName\>               |
   | notificationHubNamespace       | String                   | \<hubNamespace\>          |
    
   Необходимые значения можно найти, перейдя по адресу **концентратора уведомлений** ресурса в **портала Azure**. Можно найти **notificationHubName** и **notificationHubNamespace** значения в правом верхнем углу **Essentials** сводки в  **Общие сведения о** страницы.

   ![Сводка Essentials концентраторов уведомлений](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Можно найти **notificationHubKeyName** и **notificationHubKey** значения, перейдя по адресу **политики доступа**, чтобы выбрать соответствующие **доступа Политика**. Например, `DefaultFullSharedAccessSignature`. Скопируйте из **основной строки подключения** значения начинаются с префикса `SharedAccessKeyName=` для `notificationHubKeyName` и значения начинаются с префикса `SharedAccessKey=` для `notificationHubKey`. Строка подключения должна быть в следующем формате:
    
   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Проще говоря, вы используете *DefaultFullSharedAccessSignature* , токен можно использовать для отправки уведомлений также. Однако на практике `DefaultListenSharedAccessSignature` будет лучшим выбором для ситуаций, где вы хотите получать уведомления.       
6. В разделе **навигатор проекта**, нажмите кнопку **имя_проекта**, затем нажмите кнопку **Общие** вкладку
7. Найти **удостоверений**, задайте **идентификатор пакета** значение, чтобы он соответствовал той, которая использовалась для **идентификатор приложения** (из предыдущих шага) то есть `'com.<organization>.PushDemo'`
8. Найти **подписывание**, убедитесь, что выбран соответствующий **Team** для вашей **учетной записи разработчика Apple** (тот, с которой вы создали сертификаты и профили ранее).  **Xcode** должна автоматически извлечь вниз соответствующий **профиль подготовки** на основе **идентификатор пакета**. Если вы не видите новый **профиль подготовки**, обновите профили для **удостоверение подписывания** (*Xcode > предпочтения > учетная запись > сведения о представлении*). Щелкнув **удостоверение подписывания**, выбрав **обновить** кнопки в правом нижнем углу следует загрузить профили.
9. Выберите **возможности** вкладку и убедитесь, что **Push-уведомления** включены.
10. Откройте ваш **AppDelegate.swift** файл для реализации *UNUserNotificationCenterDelegate* протокола и добавьте следующий код в начало класса:
    
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

    Эти члены будет использоваться в дальнейшем. *Теги* и *genericTemplate* будет использоваться как часть регистрации. Дополнительные сведения о тегах см. в разделе [тегов для регистраций](notification-hubs-tags-segment-push-message.md) и [Регистрация шаблонов](notification-hubs-templates-cross-platform-push-messages.md).
 
11. В этом же файле добавьте следующий код в *didFinishLaunchingWithOptions* функции:

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

    Этот код извлекает значения параметров из **devsettings.plist**, задает **AppDelegate** классов как *UNUserNotificationCenter* делегировать, запросы авторизации для Push-уведомлений, а затем вызывает *registerForRemoteNotifications*.
    
    Проще говоря, этот код поддерживает **iOS 10 и более поздних версий только**. С условно помощью соответствующие интерфейсы API и подходы, как это обычно делается, можно добавить поддержку более ранние версии ОС.

12. В этом же файле добавьте следующие функции:

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

    Код использует *installationId* и *pushChannel* значения для регистрации с **концентратора уведомлений**. В этом случае вы используете *UIDevice.current.identifierForVendor* на уникальное значение для идентификации устройства и последующего форматирования *deviceToken* предоставления нам нужное *pushChannel* значение. *ShowAlert* функцией является просто отображается текст сообщения для демонстрационных целей.

13. По-прежнему в **AppDelegate.swift**, добавьте *willPresent* и *didReceive* **UNUserNotificationCenterDelegate** функции Отображение оповещений при поступлении уведомления, когда приложение выполняется в переднего плана и фона, соответственно
    
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

14. Добавьте в конец выражения print *didRegisterForRemoteNotificationsWithDeviceToken* функции, чтобы убедиться, что *installationId* и *pushChannel* выполняется присвоенные значения
15. Создание папок (**моделей**, **служб**, и **служебные программы**) для основных компонентов, необходимо добавить в проект позже
16. Проверьте, что построение проекта и выполняется на физическом устройстве (Push-уведомлений не может быть проверен с использованием имитатора)

### <a name="create-models"></a>Создание моделей
На этом шаге вы создадите набор моделей для представления [API REST концентраторов уведомлений](/rest/api/notificationhubs/) полезных данных, а также сохранять необходимая **маркер SAS** данных.


1.  Добавьте новый файл swift для **моделей** вызывается **PushTemplate.swift**. Эта модель предоставляет объект, представляющий структуру **текст** отдельного шаблона как часть **DeviceInstallation** полезных данных:
    
    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String
    
        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Добавьте новый файл swift для **моделей** папку с именем **DeviceInstallation.swift**. Этот файл определяет структуру, представляющий полезные данные для создания или обновления **установки устройств**. Добавьте в него указанный ниже код.
    
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

3.  Добавьте новый файл swift под **моделей** вызывается **TokenData.swift**. Эта модель будет использоваться для хранения **маркер SAS** вместе с истечения срока действия

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
**Концентраторы уведомлений** используется та же инфраструктура безопасности, как **служебной шины Azure**. Чтобы вызвать REST API, вам потребуется [программным способом создать маркер SAS](/rest/api/eventhub/generate-sas-token) , используемый в **авторизации** заголовок запроса.  

Результирующий маркер будет иметь следующий формат: 

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Сам процесс включает в себя те же действия шесть ключей:  

1.  Вычисления истечения срока действия в [виде времени эпохи UNIX](https://en.wikipedia.org/wiki/Unix_time) формат (секунды, прошедшего с момента 00:00:00 UTC 1 января 1970 года)
2.  Форматирование **ResourceUrl** (предоставляющий ресурс, который вы пытаетесь получить доступ к, то есть `'https://\<namespace\>.servicebus.windows.net/\<hubName\>'`), закодированные и в нижнем регистре
3.  Подготовка **StringToSign**, который состоит из `'\<**UrlEncodedResourceUrl**\>\n\<**ExpiryEpoch**\>'`
4.  Вычисления (и первые 64 символа) **подпись** с помощью **HMAC-SHA256** из **StringToSign** со значением **ключ** частью **Строку подключения** (для соответствующих **правило авторизации**)
5.  Форматирование в кодировке base-64 **подпись** это знаком процента
6.  Создав **маркера** в требуемом формате, используя **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**и **UrlEncodedResourceUrl** значения

См. в разделе [документации по служебной шине](../service-bus-messaging/service-bus-sas.md) полно Обзор **подписанного** и способ его использования **служебной шины Azure** и  **Концентраторы уведомлений**.

В целях этого примера Swift, вы собираетесь использовать с открытым исходным кодом Apple **CommonCrypto** библиотеки для хэширования, подписи. Так как это библиотеки, недоступна в Swift стандартных. Тем не менее это можно сделать доступными с помощью промежуточный заголовок. Чтобы добавить и настроить заголовок.

1. В **Xcode**, перейдите в меню **файл**, затем **New**, затем **файл** и выберите **заголовочный файл** Назовите его *«BridgingHeader.h»*
2. Измените файл для импорта **CommonHMAC**

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

3. Обновить целевой объект **параметры построения** для ссылки на заголовок. Откройте **стандартных параметров** вкладку и прокрутите вниз до **компилятор Swift** раздел. Убедитесь, что **установить заголовок Objective-C совместимости** параметру присвоено значение **Да** и введите путь к файлу заголовка мост в **мост заголовка Objective-C**   параметр, то есть `'\<ProjectName\>/BridgingHeader.h'`. Если не удается найти эти параметры, убедитесь, **все** выбрано представление (а не **основные** или **настроенная**).
    
   Есть много программы-оболочки для независимых производителей открытым кодом библиотеки, что может усложнить использование **CommonCrypto** более удобным. Он выходит за рамки этой статьи.

4. Добавьте новый файл Swift под **служебные программы** папку с именем **TokenUtility.swift** и добавьте следующий код:

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
    
    Эта служебная программа содержит логику, ответственными за генерирование **маркер SAS**. *GetSasToken* функция организует высокоуровневые шаги, необходимые для подготовки маркер, для которого, как описано ранее и будет вызываться службой установки в шагах этого руководства. Две другие функции вызываются *getSasToken функция*; *sha256HMac* для вычисления подписи и *urlEncodedString* для кодирования соответствующие строки URL-адреса. *UrlEncodedString* функция требовалось, так как это было невозможно получить требуемый результат с помощью встроенной *addingPercentEncoding* функции. [Пакет SDK для iOS хранилища Azure](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) служил прекрасным примером как подходить к эти операции, хотя и в Objective-C. Дополнительные сведения о **маркеры SAS служебной шины Azure** можно найти в [документации по служебной шине](../service-bus-messaging/service-bus-sas.md). 

### <a name="verify-the-sas-token"></a>Проверки маркера SAS
Прежде чем реализовать службу установки клиента, вы можете проверить, что наше приложение правильно создает **маркер SAS** вашей программе http, по выбору. В рамках этой записи, будет наше средство выбора **Postman**.

Запишите *installationId* и *маркера* значения, генерируемых приложение с помощью соответствующим образом помещено печать инструкции или точки останова. 

Выполните следующие действия для вызова *установок* API:

1. В **Postman**, откройте новую вкладку
2. Присвоить запросу **получить** и следующий адрес:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

3. Настройте заголовки запроса следующим образом:
    
   | Ключ           | Value            |
   | ------------- | ---------------- |
   | Content-Type  | приложение/json |
   | Авторизация | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. Щелкните **кода** кнопки (верхний правый под **Сохранить** кнопки). Запрос должен выглядеть аналогично приведенному ниже:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

5. Нажмите кнопку **отправки** кнопки

Регистрация не существует для указанного *installationId* на этом этапе, однако это должно привести **404 не найдено** ответа вместо **401 — не санкционировано**. Этот результат должен убедитесь, что **маркер SAS** принят.

### <a name="implement-the-installation-service-class"></a>Реализовать класс службы установки
Затем вы реализуете наши основные оболочку вокруг [установок REST API](/rest/api/notificationhubs/create-overwrite-installation).  

Добавьте новый файл Swift под **служб** папку с именем **NotificationRegistrationService.swift**, затем добавьте следующий код в этот файл:

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
 
Все необходимые подробности в процессе инициализации. Теги и шаблоны при необходимости передаются в *зарегистрировать* функции являются частью **установки устройств** полезные данные JSON.  

*Зарегистрировать* функция вызывает другие закрытые функции, чтобы подготовить запрос. После получения ответа, завершение называется, указывающее, является ли регистрация прошла успешно, или нет.  

Конечную точку запрос создается путем *getBaseAddress* функцию при помощи **концентратора уведомлений** параметры **пространства имен** и **имя**предоставляются во время инициализации.  

*GetSasToken* функция проверяет ли в настоящее время хранимый маркер является допустимым, в противном случае он будет выполнить вызов **TokenUtility** могли создать новый и сохранить его перед возвратом значения. 

Наконец *encodeToJson* преобразует объекты соответствующей модели в JSON для использования в качестве части текста запроса.

### <a name="invoke-the-notification-hubs-rest-api"></a>Вызвать REST API для центров уведомлений
Последним шагом является обновление **AppDelegate** использовать **NotifiationRegistrationService** для регистрации в нашей **NotificationHub**. 

1. Откройте **AppDelegate.swift** и добавьте переменную уровня класса для хранения ссылки на **NoficiationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

2. В этом же файле обновите *didRegisterForRemoteNotificationsWithDeviceToken* функцию для инициализации **NotificationRegistrationService** с необходимыми параметрами, затем вызовите *зарегистрировать* функции.

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
    
        // Call register passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Проще говоря, вы собираетесь использовать несколько выражения print для обновления в окне вывода с результатом *зарегистрировать* операции. 

3. Теперь сборка и запуск приложения (на физическом устройстве). Вы должны увидеть **«Registered»** в окне вывода.

## <a name="test-the-solution"></a>Тестирование решения
На этом этапе наше приложение регистрируется с **NotificationHub** и оно может получать Push-уведомлений. В **Xcode**, остановите отладчик и завершение работы приложения (если она запущена). Далее предстоит проверить, **установки устройств** сведения будут должным образом и что наше приложение действительно теперь могут получать Push-уведомлений.  

### <a name="verify-the-device-installation"></a>Проверка установки устройства
Теперь можно сделать тот же запрос, как это делалось ранее с помощью **Postman** для [проверки маркера SAS](#verify-the-sas-token). Предположим, что **маркер SAS** еще не истек, ответ должен включать сведения об установке, которое вы указали, такие как шаблоны и теги.  

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
— Самый быстрый способ проверить, что вы теперь можете получать уведомления для перехода к **концентратора уведомлений** в **портала Azure**.

1. В **портала Azure**, перейдите к **Обзор** вкладке вашей **концентратора уведомлений**
2. Нажмите кнопку **Тестовая отправка** (верхний левый) выше **Essentials** сводки

    ![Кнопка отправки сводки теста Essentials концентраторов уведомлений](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)
3. Выберите **пользовательский шаблон** из списка **платформ**
4. Введите **12345** для **Отправка к выражению тега** (вы указали этот тег в нашей установки)
5. При необходимости измените **сообщение** в полезных данных JSON
    
    ![Тестовая отправка концентраторов уведомлений](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)
6. Нажмите кнопку **отправки** и портала должно быть указано ли уведомление на устройство успешно отправлено

    ![Проверка концентраторов уведомления Отправка результатов](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Вы также увидите уведомление в **центр уведомлений** на устройстве (при условии, что приложение не выполняется на переднем плане). Щелкнув уведомление должно откройте приложение и отображать сообщение.

    ![Получено уведомление о примере](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-postman"></a>Отправить тестовое уведомление (Postman)
Вы можете отправлять уведомления с помощью соответствующих [REST API](/rest/api/notificationhubs/) через **Postman** как контейнер и он может быть более удобным способом для тестирования. 

1. В **Postman**, откройте новую вкладку
2. Присвоить запросу **POST** и введите следующий адрес:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

3. Настройте заголовки запроса следующим образом:
    
   | Ключ                            | Value                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Авторизация                  | \<sasToken\>                   |
   | Формат ServiceBusNotification  | шаблон                       |
   | Tags                           | 12345                        |

4. Настройте запрос **текст** использовать **RAW - JSON (application.json)** с следующие полезные данные JSON:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

5. Щелкните **кода** кнопки (верхний правый под **Сохранить** кнопки). Запрос должен выглядеть аналогично приведенному ниже:

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

5. Нажмите кнопку **отправки** кнопки

Следует получить код состояния успеха и получать уведомления на клиентском устройстве.

## <a name="next-steps"></a>Дальнейшие действия
Теперь у вас есть основные iOS Swift приложения, подключенного к **концентратора уведомлений** через [REST API](/rest/api/notificationhubs/) и может отправлять и получать уведомления. Дополнительные сведения см. в следующих статьях: 

- [Общие сведения о центрах уведомлений Azure](notification-hubs-push-notification-overview.md)
- [Использование интерфейса REST центров уведомлений](/rest/api/notificationhubs/)
- [Пакет SDK для центров уведомлений для серверных операций](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Концентраторы уведомлений SDK на сайте GitHub](https://github.com/Azure/azure-notificationhubs)
- [Регистрация с помощью серверной части приложения](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Управление регистрацией](notification-hubs-push-notification-registration-management.md)
- [Работа с тегами](notification-hubs-tags-segment-push-message.md) 
- [Работа с пользовательскими шаблонами](notification-hubs-templates-cross-platform-push-messages.md)
- [Управление доступом служебной шины с подписями коллективного доступа](../service-bus-messaging/service-bus-sas.md)
- [Программное создание маркеров SAS](/rest/api/eventhub/generate-sas-token)
- [Apple безопасности: общие crypto](https://developer.apple.com/security/)
- [Время начала эпохи UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
