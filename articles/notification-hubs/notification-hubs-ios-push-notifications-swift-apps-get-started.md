---
title: Push-уведомления для приложений SWIFT iOS, использующих центры уведомлений Azure | Документация Майкрософт
description: Узнайте, как отправлять уведомления в приложения SWIFT для iOS, использующие центры уведомлений Azure.
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
ms.openlocfilehash: 8dae5bcc082ba5dd0953e3e97f609e4031547a35
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030654"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>Учебник. push-уведомления для приложений SWIFT iOS, использующих центры уведомлений REST API

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

В этом руководстве вы используете центры уведомлений Azure для отправки уведомлений в приложение iOS на основе SWIFT с помощью [REST API](/rest/api/notificationhubs/). Вы также создаете пустое приложение iOS, которое получает push-уведомления с помощью [службы push-уведомлений Apple (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

В этом учебнике выполняются следующие действия.

> [!div class="checklist"]
> * Создание файла запроса подписи сертификата.
> * Запросите ваше приложение для отправки push-уведомлений.
> * Создайте профиль подготовки для приложения.
> * Создание центра уведомлений.
> * Настройка центра уведомлений с помощью сведений APNs.
> * Подключите приложение iOS к концентратору уведомлений.
> * Протестируйте решение.

## <a name="prerequisites"></a>предварительным требованиям

Для работы вам потребуется следующее:

- Чтобы пройти [Обзор центров уведомлений Azure](notification-hubs-push-notification-overview.md) , если вы не знакомы со службой.
- Для получения сведений о [регистрации и установке](notification-hubs-push-notification-registration-management.md).
- Активная [учетная запись разработчика Apple](https://developer.apple.com).
- Mac-адрес, на котором работает Xcode, а также действительный сертификат разработчика, установленный в цепочку ключей.
- Физическое устройство iPhone, которое можно запустить и выполнить отладку с помощью, так как вы не можете протестировать push-уведомления с симулятором.
- Физическое устройство iPhone, зарегистрированное на [портале Apple](https://developer.apple.com) и связанное с сертификатом.
- [Подписка Azure](https://portal.azure.com) , в которой можно создавать ресурсы и управлять ими.

Даже если у вас нет опыта разработки с iOS, вы сможете выполнить инструкции по созданию этого примера первого принципа. Тем не менее вы получите преимущества от ознакомления со следующими понятиями:

- Создание приложений iOS с помощью Xcode и SWIFT.
- Настройка [центра уведомлений Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) для iOS.
- [Портал разработчика Apple](https://developer.apple.com) и [портал Azure](https://portal.azure.com).

> [!NOTE]
> Центр уведомлений будет настроен для использования только режима проверки подлинности **"песочницы"** . Этот режим проверки подлинности не следует использовать для рабочих нагрузок в рабочей среде.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Подключение приложения iOS к концентратору уведомлений

В этом разделе вы создадите приложение iOS, которое будет подключаться к концентратору уведомлений.  

### <a name="create-an-ios-project"></a>Создание проекта iOS

1. В XCode создайте новый проект iOS и выберите шаблон **Single View Application** (Приложение с одним представлением).

1. При настройке параметров для нового проекта:

   1. Укажите **Название продукта** (пушдемо) и **идентификатор организации** (`com.<organization>`), которые использовались при установке **идентификатора пакета** на портале разработчика Apple.

   1. Выберите **группу** , для которой был настроен **идентификатор приложения** .

   1. Задайте для параметра **язык** значение **SWIFT**.

   1. Нажмите кнопку **Далее**.

1. Создайте новую папку с именем **суппортингфилес**.

1. Создайте в папке **суппортингфилес** новый файл p-List с именем **девсеттингс. plist** . Не забудьте добавить эту папку в файл **gitignore** , чтобы она не зафиксировалась при работе с репозиторием Git. В рабочем приложении, скорее всего, эти секреты будут заданы в ходе автоматизированного процесса сборки. Такие параметры не рассматриваются в этом пошаговом руководстве.

1. Обновите **девсеттингс. plist** , чтобы включить следующие записи конфигурации, используя собственные значения из подготовленного центра уведомлений:

   | Key                            | введите                     | Значение                     |
   |--------------------------------| -------------------------| --------------------------|
   | нотификатионхубкэй             | Строка,                   | \<Хубкэй >                  |
   | нотификатионхубкэйнаме         | Строка,                   | \<Хубкэйнаме >              |
   | нотификатионхубнаме            | Строка,                   | \<hubName >                 |
   | нотификатионхубнамеспаце       | Строка,                   | \<Хубнамеспаце >            |

   Необходимые значения можно найти, перейдя к ресурсу центра уведомлений в портал Azure. В частности, значения **нотификатионхубнаме** и **нотификатионхубнамеспаце** находятся в правом верхнем углу сводки **основных** компонентов на странице **обзора** .

   ![Сводка по Essentials по концентраторам уведомлений](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Вы также можете найти значения **нотификатионхубкэйнаме** и **нотификатионхубкэй** , перейдя к **политикам доступа** и выбрав соответствующую **политику доступа**, например `DefaultFullSharedAccessSignature`. После этого скопируйте из **основной строки подключения** значение с префиксом `SharedAccessKeyName=` для `notificationHubKeyName` и значение с префиксом `SharedAccessKey=` для `notificationHubKey`.

   Строка подключения должна иметь следующий формат:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Чтобы не усложнять его, укажите `DefaultFullSharedAccessSignature`, чтобы можно было использовать маркер для отправки уведомлений. На практике `DefaultListenSharedAccessSignature` лучше всего подходит для случаев, когда требуется получать уведомления.

1. В **навигаторе по проекту**выберите **имя проекта** и перейдите на вкладку **Общие** .

1. Найдите **удостоверение** , а затем задайте значение **идентификатора пакета** таким образом, чтобы оно соответствовало `com.<organization>.PushDemo`, которое является значением, используемым для **идентификатора приложения** из предыдущего шага.

1. Найдите **Подписывание**, а затем выберите соответствующую **команду** для вашей **учетной записи разработчика Apple**. Значение **команды** должно совпадать с тем, в котором были созданы сертификаты и профили.

1. Xcode должен автоматически извлечь соответствующее значение **профиля подготовки** на основе **идентификатора пакета**. Если значение нового **профиля подготовки** не отображается, попробуйте обновить профили для **удостоверения подписывания** , выбрав **Xcode** > **предпочтения** > **учетная запись** > **просмотра сведений**. Выберите **удостоверение подписывания**, а затем нажмите кнопку **Обновить** в правом нижнем углу, чтобы скачать профили.

1. Перейдите на вкладку **возможности** и убедитесь, что **Push-уведомления** включены.

1. Откройте файл **AppDelegate. SWIFT** , чтобы реализовать протокол **UNUserNotificationCenterDelegate** , и добавьте следующий код в начало класса:

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

    Эти члены будут использоваться позже. В частности, в процессе регистрации вы будете использовать **теги** и элементы **женериктемплате** . Дополнительные сведения о тегах см. в разделе [теги для](notification-hubs-tags-segment-push-message.md) регистраций и [регистрации шаблонов](notification-hubs-templates-cross-platform-push-messages.md).

1. В том же файле добавьте в функцию **didFinishLaunchingWithOptions** следующий код:

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

    Этот код извлекает значения параметров из **девсеттингс. plist**, устанавливает класс **AppDelegate** в качестве делегата **унусернотификатионцентер** , запрашивает авторизацию для push-уведомлений, а затем вызывает **регистерфорремотенотификатионс**.

    Для простоты код поддерживает *iOS 10 и более поздних версий*. Можно добавить поддержку предыдущих версий ОС, условно используя соответствующие API и подходы, как обычно.

1. В том же файле добавьте следующие функции:

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

    Код использует значения **installationId** и **пушчаннел** для регистрации в центре уведомлений. В этом случае вы используете **уидевице. Current. идентифиерфорвендор** , чтобы предоставить уникальное значение для распознавания устройства, а затем отформатировать **девицетокен** , чтобы предоставить нужное значение **пушчаннел** . Функция **шовалерт** существует просто для вывода текста сообщения в демонстрационных целях.

1. По-прежнему в файле **AppDelegate. SWIFT** добавьте функции **виллпресент** и **дидрецеиве** в **UNUserNotificationCenterDelegate**. Эти функции отображают оповещение о том, что приложение выполняется либо на переднем плане, либо в фоновом режиме соответственно.

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

1. Добавьте инструкции PRINT в нижнюю часть функции **didRegisterForRemoteNotificationsWithDeviceToken** , чтобы убедиться, что значения **installationId** и **пушчаннел** назначаются.

1. Создайте папки **Models**, **Services**и **Utilities** для базовых компонентов, которые позже будут добавлены в проект.

1. Убедитесь, что проект строится и выполняется на физическом устройстве. Push-уведомления нельзя тестировать с помощью симулятора.

### <a name="create-models"></a>Создание моделей

На этом шаге вы создадите набор моделей, представляющих [концентраторы уведомлений REST API](/rest/api/notificationhubs/) полезные данные, и для хранения необходимых данных маркера подписанного URL-адрес (SAS).

1. Добавьте новый SWIFT-файл с именем **пуштемплате. SWIFT** в папку **Models** . Эта модель предоставляет структуру, представляющую **тело** отдельного шаблона в составе полезных данных **девицеинсталлатион** .

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Добавьте новый SWIFT-файл с именем **девицеинсталлатион. SWIFT** в папку **Models** . Этот файл определяет структуру, представляющую полезные данные для создания или обновления **установки устройства**. Добавьте в него указанный ниже код.

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

1. Добавьте новый SWIFT-файл с именем **токендата. SWIFT** в папку **Models** . Эта модель будет использоваться для хранения маркера SAS вместе с сроком действия.

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

Концентраторы уведомлений используют ту же инфраструктуру безопасности, что и служебная шина Azure. Чтобы вызвать REST API, необходимо [программно создать маркер SAS](/rest/api/eventhub/generate-sas-token) , который можно использовать в заголовке **авторизации** запроса.  

Полученный маркер будет иметь следующий формат:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Сам процесс включает в себя те же шесть основных шагов:  

1. Вычисление срока действия в формате [времени работы UNIX](https://en.wikipedia.org/wiki/Unix_time) , которое означает число секунд, прошедших с полуночи в формате UTC, 1 января 1970 г.
1. Форматирование **ResourceUrl** , представляющее ресурс, к которому вы пытаетесь получить доступ, чтобы он был закодирован и записан в нижний регистр. **ResourceUrl** имеет форму `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Подготовка **стрингтосигн**, которая отформатирована как `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`.
1. Вычисление и кодирование **сигнатуры** в кодировке Base64 с помощью хэша HMAC-SHA256 значения **стрингтосигн** . Хэш-значение используется с **ключевой** частью **строки подключения** для соответствующего **правила авторизации**.
1. Форматирование **подписи** в кодировке Base64 для кодирования в процентах.
1. Создание маркера в ожидаемом формате с помощью значений **урленкодедсигнатуре**, **експирепоч**, **keyName**и **урленкодедресаурцеурл** .

Более подробный обзор подписанного URL-канала и его использования в служебной шине и центрах уведомлений Azure см. в [документации по служебной шине Azure](../service-bus-messaging/service-bus-sas.md) .

В этом примере SWIFT вы будете использовать библиотеку **коммонкрипто** с открытым кодом Apple, чтобы помочь в хэшировании сигнатуры. Так как это библиотека C, она недоступна в окне SWIFT. Библиотеку можно сделать доступной с помощью заголовка моста.

Чтобы добавить и настроить заголовок моста, сделайте следующее:

1. В Xcode выберите **файл** > **создать** > **файл** > **заголовочный файл**. Назовите файл заголовка **бридгингхеадер. h**.

1. Измените файл, чтобы импортировать **коммонхмак. h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Обновите **параметры сборки** целевого объекта, чтобы они ссылались на заголовок моста:

   1. Откройте вкладку **параметры здания** и прокрутите вниз до раздела **компилятора SWIFT** .

   1. Убедитесь, что для параметра **установить цель установки в заголовке совместимости** с выбрано значение **Да**.

   1. Введите путь к файлу, `'<ProjectName>/BridgingHeader.h'` в параметр **заголовков "Целевая привязка-C** ". Это путь к файлу для заголовка моста.

   Если вы не можете найти эти параметры, убедитесь, что выбрано **все представление все** , а не **базовое** или **настроенное**.

   Существует множество сторонних библиотек оболочек с открытым исходным кодом, которые могут сделать использование **коммонкрипто** немного проще. Однако обсуждение таких библиотек выходит за рамки этой статьи.

1. Добавьте новый файл SWIFT с именем **токенутилити. SWIFT** в папку **Служебные программы** и добавьте следующий код:

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

   Эта служебная программа инкапсулирует логику, отвечающую за создание маркера SAS.

   Как было описано ранее, функция **жетсастокен** управляет действиями высокого уровня, необходимыми для подготовки маркера. Эта функция будет вызываться службой установки далее в этом руководстве.

   Две другие функции вызываются функцией **жетсастокен** : **sha256HMac** для вычисления сигнатуры и **урленкодедстринг** для кодирования связанной строки URL-адреса. Функция **урленкодедстринг** является обязательной, так как невозможно достичь необходимых выходных данных с помощью встроенной функции **аддингперцентенкодинг** .

   [Пакет SDK для iOS в службе хранилища Azure](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) — отличный пример подхода к этим операциям в цели-C. Дополнительные сведения о маркерах SAS служебной шины Azure можно найти в [документации по служебной шине Azure](../service-bus-messaging/service-bus-sas.md).

### <a name="verify-the-sas-token"></a>Проверка маркера SAS

Перед реализацией службы установки в клиенте убедитесь, что наше приложение правильно создает маркер SAS с помощью выбранной вами программы HTTP. В рамках этого руководства наша программа выбора будет **публиковать**.

Используйте соответствующую инструкцию PRINT или точку останова, чтобы отметить **installationId** и значения **токенов** , создаваемые приложением.

Чтобы вызвать API **установки** , выполните следующие действия.

1. В окне **POST (публикация**) откройте новую вкладку.

1. Задайте запрос на **Получение** и укажите следующий адрес:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Настройте заголовки запроса следующим образом:

   | Key           | Значение            |
   | ------------- | ---------------- |
   | Content-Type  | приложение/json |
   | Авторизация | \<sasToken >       |
   | x-ms-version  | 2015-01          |

1. Нажмите кнопку **код** , которая отображается в правом верхнем углу под кнопкой **сохранить** . Запрос должен выглядеть, как в следующем примере:

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

На этом этапе регистрация для указанного **installationId** не существует. Проверка должна привести к отклику "404 не найдено", а не к ответу "401 не авторизован". Этот результат должен подтвердить, что маркер SAS принят.

### <a name="implement-the-installation-service-class"></a>Реализация класса службы установки

Далее вы реализуете нашу базовую оболочку для [установки REST API](/rest/api/notificationhubs/create-overwrite-installation).  

Добавьте новый SWIFT-файл с именем **нотификатионрегистратионсервице. SWIFT** в папке **Services** , а затем добавьте в этот файл следующий код:

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

Сведения о необходимых компонентах предоставляются в процессе инициализации. Теги и шаблоны при необходимости передаются в функцию **Register** для формирования части полезных данных JSON **установки устройства** .  

Функция **Register** вызывает другие закрытые функции для подготовки запроса. После получения ответа вызывается завершение и показывается, была ли регистрация успешной.  

Конечная точка запроса создается функцией **getBaseAddress** . В конструкции используется *пространство имен* параметров концентратора уведомлений и *имя* , предоставленное во время инициализации.  

Функция **жетсастокен** проверяет, является ли текущий сохраненный токен допустимым. Если маркер является недопустимым, функция вызывает **токенутилити** для создания нового маркера и сохраняет его перед возвратом значения.

Наконец, **енкодетожсон** преобразует соответствующие объекты модели в формат JSON для использования в качестве части текста запроса.

### <a name="invoke-the-notification-hubs-rest-api"></a>Вызов концентраторов уведомлений REST API

Последним шагом является обновление **AppDelegate** , чтобы использовать **нотификатионрегистратионсервице** для регистрации в **NotificationHub**.

1. Откройте **AppDelegate. SWIFT** и добавьте переменную уровня класса, чтобы сохранить ссылку на **нотификатионрегистратионсервице**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. В том же файле обновите функцию **didRegisterForRemoteNotificationsWithDeviceToken** , чтобы инициализировать **нотификатионрегистратионсервице** с параметрами реквизита, а затем вызовите функцию **Register** .

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

    Чтобы не усложнять это, вы будете использовать несколько инструкций Print, чтобы обновить окно вывода с учетом результата операции **регистрации** .

1. Теперь создайте и запустите приложение на физическом устройстве. В окне вывода должно отобразиться «зарегистрировано».

## <a name="test-the-solution"></a>Тестирование решения

Наше приложение на этом этапе регистрируется в **NotificationHub** и может получать push-уведомления. В Xcode завершите работу отладчика и закройте приложение, если оно выполняется в данный момент. Затем убедитесь, что сведения об **установке устройства** должны быть ожидаемыми, а теперь приложение может получать push-уведомления.  

### <a name="verify-the-device-installation"></a>Проверка установки устройства

Теперь можно выполнить тот же запрос, что и ранее, с помощью **процедуры POST** для [проверки маркера SAS](#verify-the-sas-token). Предполагая, что срок действия маркера SAS не истек, ответ должен содержать предоставленные сведения об установке, такие как шаблоны и теги.

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

### <a name="send-a-test-notification-azure-portal"></a>Отправка тестового уведомления (портал Azure)

Самый быстрый способ проверить, что теперь можно получать уведомления, — перейти в центр уведомлений на портал Azure:

1. В портал Azure перейдите на вкладку **Обзор** в центре уведомлений.

1. Выберите **Тестовая Отправка**, которая находится над сводкой **основные компоненты** в верхнем левом углу окна портала:

    ![Кнопка отправки сводки по центрам уведомлений Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Выберите **Пользовательский шаблон** в списке **платформы** .

1. Введите **12345** в поле **выражение для тега Send to**. Вы ранее указали этот тег в нашей установке.

1. При необходимости измените **сообщение** в полезных данных JSON:

    ![Тестовая отправка для концентраторов уведомлений](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Нажмите кнопку **Отправить**. На портале должно быть указано, было ли уведомление успешно отправлено на устройство:

    ![Результаты отправки тестов концентраторов уведомлений](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Если приложение не выполняется на переднем плане, вы также увидите уведомление в **центре уведомлений** на устройстве. При касании уведомления открывается приложение и отображается оповещение.

    ![Пример получения уведомления](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Отправка тестового уведомления (перевозчик почты)

Уведомления можно отправлять через [REST API](/rest/api/notificationhubs/) с помощью **процедуры POST**, что может оказаться более удобным способом тестирования.

1. Откройте новую вкладку в **POST**.

1. Задайте запрос **POST**и введите следующий адрес:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Настройте заголовки запроса следующим образом:

   | Key                            | Значение                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Авторизация                  | \<sasToken >                     |
   | Сервицебуснотификатион-формат  | шаблон                       |
   | Теги                           | 12345                        |

1. Настройте **текст** запроса для использования **RAW-JSON (Application. JSON)** со следующими полезными данными JSON:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Нажмите кнопку **код** , расположенную под кнопкой **сохранить** в правом верхнем углу окна. Запрос должен выглядеть, как в следующем примере:

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

Вы должны получить код состояния успеха и получить уведомление на клиентском устройстве.

## <a name="next-steps"></a>Дополнительная информация
Теперь у вас есть базовое приложение iOS SWIFT, подключенное к концентратору уведомлений через [REST API](/rest/api/notificationhubs/) и позволяющее отправлять и получать уведомления. Дополнительные сведения см. в следующих статьях:

- [Общие сведения о центрах уведомлений Azure](notification-hubs-push-notification-overview.md)
- [Использование интерфейса REST центров уведомлений](/rest/api/notificationhubs/)
- [Пакет SDK для концентраторов уведомлений для серверных операций](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Пакет SDK для концентраторов уведомлений на GitHub](https://github.com/Azure/azure-notificationhubs)
- [Регистрация в серверной части приложения](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Управление регистрацией](notification-hubs-push-notification-registration-management.md)
- [Работа с тегами](notification-hubs-tags-segment-push-message.md) 
- [Работа с пользовательскими шаблонами](notification-hubs-templates-cross-platform-push-messages.md)
- [Управление доступом к служебной шине с помощью подписей общего доступа](../service-bus-messaging/service-bus-sas.md)
- [Программное создание маркеров SAS](/rest/api/eventhub/generate-sas-token)
- [Безопасность Apple: общее шифрование](https://developer.apple.com/security/)
- [Время эпохи в UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
