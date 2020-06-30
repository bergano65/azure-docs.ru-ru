---
title: Отправка push-уведомлений в приложения Swift для iOS с помощью Центров уведомлений Azure и REST API
description: Из этого руководства вы узнаете, как отправлять push-уведомления на устройства iOS с помощью Центров уведомлений Azure и REST API.
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/03/2020
ms.openlocfilehash: 861011874c63f4c5d18ce33b107bbe9a80359045
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2020
ms.locfileid: "85127474"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-apis"></a>Руководство по отправке push-уведомлений в приложения SWIFT для iOS с помощью Центров уведомлений и REST API

В этом учебнике описывается, как отправлять push-уведомления в приложения iOS с помощью центров уведомлений Azure и REST API.

В этом руководстве рассматриваются следующие действия:

- создание примера приложения iOS;
- подключение приложения iOS к Центрам уведомлений Azure;
- отправка тестовых push-уведомлений;
- проверка получения уведомлений приложением.

Полный код для этого руководства можно скачать на сайте [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples).

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством вам потребуется:

- компьютер Mac с [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), а также действительный сертификат разработчика, установленный в цепочку ключей;

- устройство iPhone или iPad под управлением iOS 10 или более поздней версии;

- регистрация физического устройства на [портале Apple](https://developer.apple.com/)  и настройка связи с сертификатом.

Прежде чем продолжить, обязательно пройдите предыдущее руководство по началу работы с [Центрами уведомлений Azure для приложений iOS](https://go.microsoft.com/fwlink/?linkid=2129801), чтобы создать и настроить учетные данные для отправки push-уведомлений в центре уведомлений. Вы сможете выполнить следующие действия, даже если у вас нет опыта разработки в iOS.

> [!NOTE]
> В соответствии с требованиями к настройке push-уведомлений необходимо развернуть push-уведомления и протестировать их на физическом устройстве под управлением iOS (iPhone или iPad), но не в эмуляторе iOS.

В следующих разделах описано, как создать приложение iOS, которое подключается к Центру уведомлений.

## <a name="create-an-ios-project"></a>Создание проекта iOS

1. В Xcode создайте новый проект iOS и выберите шаблон  **Single View Application** (Приложение с одним представлением).

2. При настройке параметров для нового проекта:
   - Укажите **Имя продукта** (PushDemo) и **Идентификатор организации** (`com.<organization>`), использованные для **Идентификатора пакета** на Портале разработчика.
   - Выберите **Команду** , для которой был настроен **идентификатор приложения** .
   - Задайте для параметра  **Язык** значение **Swift**.
   - Нажмите кнопку **Далее**.

3. Создайте новую папку с именем **SupportingFiles**.

4. Создайте новый файл p-list с именем **devsettings.plist** в папке **SupportingFiles** . Не забудьте добавить эту папку в файл **gitignore** , чтобы он не зафиксировался при работе с репозиторием Git. В рабочем приложении эти секреты можно задавать с условиями как часть автоматизированного процесса сборки. Эти параметры в этом учебнике не рассматриваются.

5. Обновите **devsettings.plist** , чтобы добавить следующие записи конфигурации, используя собственные значения из подготовленного центра уведомлений:

   | **Key**                  | **Тип** | **Значение**        |
   | ------------------------ | -------- | ---------------- |
   | notificationHubKey       | Строка   | `<hubKey>`       |
   | notificationHubKeyName   | Строка   | `<hubKeyName>`   |
   | notificationHubName      | Строка   | `<hubName>`      |
   | notificationHubNamespace | Строка   | `<hubNamespace>` |

   Необходимые значения можно найти, перейдя к ресурсу центра уведомлений на портале Azure. В частности, значения  **notificationHubName** и **notificationHubNamespace** можно найти в верхнем правом углу сводки  **Главное** на странице **Обзор** .

   :::image type="content" source="media/ios-sdk-swift-rest/image1.png" alt-text="Необходимые значения":::

   Также можно найти значения  **notificationHubKeyName** и **notificationHubKey** , перейдя в раздел  **Политики доступа** и выбрав соответствующую **Политику доступа**, такую как **DefaultFullSharedAccessSignature**. После этого скопируйте значение параметра  **Первичная строка подключения** с префиксом `SharedAccessKeyName=` для **notificationHubKeyName**, и значение с префиксом  `SharedAccessKey=` для **notificationHubKey**. Строка подключения должна иметь следующий формат:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Для простоты укажите **DefaultFullSharedAccessSignature**, чтобы можно было использовать маркер для отправки уведомлений. На практике для ситуаций, в которых нужно получать уведомления, лучше всего подходит **DefaultListenSharedAccessSignature** .

6. В разделе **Project Navigator**(Навигатор по проектам) выберите **Имя проекта**  и перейдите на вкладку **Общие** .

7. Найдите **Удостоверение** и задайте значение параметра **Идентификатор пакета** , чтобы оно совпадало с `com.<organization>.PushDemo`, которое является значением, используемым для **идентификатора приложения** из предыдущего шага.

8. Найдите **Подписывание и возможности**, а затем выберите соответствующую **Команду** для **учетной записи разработчика Apple**. Значение поля  **Команда**должно совпадать со значением, с которым были созданы сертификаты и профили.

9. Xcode должен автоматически загрузить соответствующее значение **Профиля подготовки** на основе  **Идентификатора пакета**. Если вы не видите новое значение  **Профиля подготовки** , попробуйте обновить профили для  **Удостоверение подписывания** , выбрав **Xcode**, затем выберите  **Настройки**, **Учетная запись**, а затем нажмите кнопку  **Download Manual Profiles** (Загрузить профили вручную), чтобы загрузить профили.

10. По-прежнему на вкладке  **Подписывание и возможности** , нажмите кнопку **Добавить возможность** и в списке дважды коснитесь элемента **Push-уведомления** , чтобы убедиться, что **Push-уведомления** включены.

11. Откройте файл **AppDelegate.swift** для реализации протокола **UNUserNotificationCenterDelegate** и добавьте следующий код в начало класса:

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

    Эти члены будут использоваться позже. В частности, вы будете использовать элемент **теги**  как часть регистрации с помощью **настраиваемого шаблона**. Дополнительные сведения о тегах см. в разделе [Теги для регистрации](notification-hubs-tags-segment-push-message.md) и [Регистрация с помощью шаблонов](notification-hubs-templates-cross-platform-push-messages.md).

12. В том же файле добавьте в функцию **didFinishLaunchingWithOptions** следующий код:

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

    Этот код извлекает параметры из **devsettings.plist**, задает класс **AppDelegate** в качестве делегата **UNUserNotificationCenter** , запрашивает авторизацию для push-уведомлений, а затем вызывает **registerForRemoteNotifications**.

    Для простоты изложения код поддерживает только iOS 10 и более поздних версий. Можно добавить поддержку предыдущих версий iOS, условно используя соответствующие API и подходы, как это делается обычно.

13. В этом же файле добавьте следующий код:

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

    В этом коде для регистрации в Центре уведомлений используются значения **installationId** и **pushChannel** . В этом случае вы используете **UIDevice.current.identifierForVendor** , чтобы предоставить уникальное значение для обнаружения устройства и последующего форматирования **deviceToken** для получения требуемого значения **pushChannel** . Функция **showAlert** существует просто для вывода текста сообщения в демонстрационных целях.

14. По-прежнему в файле **AppDelegate.swift** добавьте функции **willPresent** and **didReceive** в **UNUserNotificationCenterDelegate**. Эти функции отображают оповещение о том, что приложение выполняется либо на переднем плане, либо в фоновом режиме.

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

15. Добавьте инструкции `print` в нижнюю часть функции **didRegisterForRemoteNotificationsWithDeviceToken** , чтобы убедиться в том, что **installationId** и **pushChannel** присвоены значения:

    ```swift
    print(installationId)
    print(pushChannel)
    ```

16. Создайте папки  **Models**, **Services** и **Utilities** для базовых компонентов, которые будут добавлены в проект позже.

17. Убедитесь, что проект собирается и выполняется на физическом устройстве. Push-уведомления нельзя проверить с помощью симулятора.

## <a name="create-models"></a>Создание моделей

На этом шаге вы создадите набор моделей для представления полезных данных [REST API Центров уведомлений](/rest/api/notificationhubs/) , а также для хранения необходимых данных маркера подписанного URL-адреса (SAS).

1. Добавьте новый файл Swift с именем **PushTemplate.swift** в папку **Models** . Эта модель определяет структуру, представляющую  **тело** отдельного шаблона в составе полезных данных **DeviceInstallation** .

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Добавьте новый файл Swift с именем **DeviceInstallation.swift** в папку **Models** . Этот файл определяет структуру, представляющую полезные данные для создания или обновления  **Установки устройства**. Добавьте в него указанный ниже код.

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

3. Добавьте новый файл Swift с именем **TokenData.swift** в папку **Models** . Эта модель используется для хранения маркера SAS вместе с его сроком действия. Добавьте в него указанный ниже код.

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

## <a name="generate-a-sas-token"></a>Создание маркера SAS

Центры уведомлений используют ту же инфраструктуру безопасности, что и Служебная шина Azure. Чтобы вызвать REST API, [программным способом создайте маркер SAS](/rest/api/eventhub/generate-sas-token) , который можно использовать в заголовке запроса **Authorization** .

Полученный маркер будет иметь следующий формат:

```xml
SharedAccessSignature sig=\<UrlEncodedSignature\>\&se=\<ExpiryEpoch\>\&skn=\<KeyName\>\&sr=\<UrlEncodedResourceUri\> |
```

Сам процесс состоит из тех же шести шагов:

1. Расчет срока действия в формате  [UNIX Epoch](https://en.wikipedia.org/wiki/Unix_time) , что означает количество секунд, прошедших с полуночи 1 января 1970 года в формате UTC.

2. Форматирование **ResourceUrl** , представляющего ресурс, к которому вы пытаетесь получить доступ, чтобы он был закодирован с использованием процентов и в нижнем регистре.  **ResourceUrl** имеет формат `https://<namespace>.servicebus.windows.net/<hubName>`.

3. Подготовка **StringToSign**, отформатированного как `<UrlEncodedResourceUrl>\n<ExpiryEpoch>`.

4. Расчет и кодирование (с использованием Base64)  **Сигнатуры** с помощью хэша HMAC-SHA256 значения **StringToSign** . Значение хэша используется с частью  **Ключ**  **строки подключения** для соответствующего **Правила авторизации**.

5. Форматирование закодированной (с использованием Base64)  **Сигнатуры** , чтобы она была закодирована с использованием процентов.

6. Создайте токен в ожидаемом формате, используя значения **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName** и **UrlEncodedResourceUrl** .

Более подробные сведения о подписанных URL-ресурсах, а также об использовании служебной шины и центров уведомлений Azure см. в  [документации по Служебной шине Azure](../service-bus-messaging/service-bus-sas.md) .

В этом примере Swift для хеширования подписи используется библиотека Apple с открытым исходным кодом  **CommonCrypto** . Так как это библиотека C, она недоступна в Swift без подготовки. Библиотеку можно сделать доступной с помощью стыковочного заголовка.

Чтобы добавить и настроить стыковочный заголовок, сделайте следующее:

1. В Xcode выберите **Файл**, затем **Создать**, **Файл**, а затем выберите **Файл заголовка**. Присвойте файлу заголовка имя  **BridgingHeader.h**.

2. Измените файл, чтобы импортировать **CommonHMAC.h**:

   ```swift
   #import <CommonCrypto/CommonHMAC.h>

   #ifndef BridgingHeader_h
   #define BridgingHeader_h

   #endif /* BridgingHeader_h */
   ```

3. Обновите целевой **Параметры сборки** для ссылки на стыковочный заголовок:

   1. Выберите проект **PushDemo** и прокрутите вниз до раздела **Компилятор Swift** .

   2. Убедитесь, что для параметра **Install Objective-C Compatibility Header** (Установить заголовок совместимости Objective-C) установлено значение  **Да**.

   3. Введите путь к файлу `<ProjectName>/BridgingHeader.h` в параметре **Objective-C bridging Header ** (Стыковочный заголовок Objective-C). Это путь к файлу стыковочного заголовка.

   Если вы не можете найти эти параметры, убедитесь, что выбрано представление  **Все** , а не **Упрощенный** или **Настраиваемый**.

   Существует множество сторонних библиотек оболочек с открытым кодом, которые могут сделать использование **CommonCrypto** немного проще. Однако обсуждение этих библиотек выходит за рамки этой статьи.

4. Добавьте новый файл Swift с именем **TokenUtility.swift** в папку **Models** , а затем добавьте следующий код:

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

   Как описывалось ранее, функция **getSasToken** управляет действиями высокого уровня, необходимыми для подготовки маркера. Эта функция будет вызываться службой установки далее в этом руководстве.

   Две другие функции вызываются функцией  **getSasToken**:  **sha256HMac** для вычисления сигнатуры и **urlEncodedString** для кодирования связанной строки URL-адреса. Функция **urlEncodedString** является обязательной, так как невозможно получить необходимые результаты с помощью встроенной функции **addingPercentEncoding** .

    [Пакет SDK службы хранилища Azure для iOS](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m)  — это отличный пример того, как реализовать эти операции в Objective-C. Дополнительные сведения о маркерах SAS служебной шины Azure см. в  [документации по служебной шине Azure](../service-bus-messaging/service-bus-sas.md).

5. В файле **AppDelegate.swift**добавьте следующий код в функцию `didRegisterForRemoteNotificationsWithDeviceToken` , чтобы убедиться, что **TokenUtility.getSasToken** создает допустимый токен.

   ```swift
   let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

   let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                            withKeyName: self.notificationHubKeyName!,
                                            andKey: self.notificationHubKey!)

   print(tokenData.token)
   ```

   Не забудьте заменить заполнители в строке **baseAddress** собственными значениями.

## <a name="verify-the-sas-token"></a>Проверка маркера SAS

Перед реализацией службы установки в клиенте убедитесь, что приложение правильно создает маркер SAS с помощью служебной программы HTTP. В рамках этого учебника мы выбираем **Postman**.

Запишите значения **installationId** и **Token** , создаваемые приложением.

Выполните следующие действия, чтобы вызвать API  **установки** :

1. В **Postman**откройте новую вкладку.
2. Задайте для запроса значение  **GET** и укажите следующий адрес:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
   ```

3. Настройте заголовки запроса следующим образом:

   | **Key**       | **Значение**        |
   | ------------- | ---------------- |
   | Content-Type  | приложение/json |
   | Авторизация | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. Нажмите кнопку **Код**  в правом верхнем углу под кнопкой  **Сохранить** . Запрос должен выглядеть следующим образом:

   ```xml
   GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
   Host: <namespace>.servicebus.windows.net
   Content-Type: application/json
   Authorization: <sasToken>
   x-ms-version: 2015-01
   Cache-Control: no-cache
   Postman-Token: <postmanToken>
   ```

5. Нажмите кнопку  **Отправить** .

На этом этапе не существует регистрации для указанного **installationId** . Проверка должна привести к ответу "404 Not Found", а не к ответу "401 Unauthorized". Этот результат должен подтвердить, что маркер SAS принят.

## <a name="implement-the-installation-service-class"></a>Реализация класса службы установки

Затем реализуйте базовую оболочку для [установок REST API](/rest/api/notificationhubs/create-overwrite-installation).

Добавьте новый файл Swift с именем **NotificationRegistrationService.swift** в папку **Services** , а затем добавьте в этот файл следующий код:

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

Сведения о необходимых компонентах предоставляются в процессе инициализации. Теги и шаблоны при необходимости передаются в функцию **register** , чтобы сформировать часть полезных данных JSON  **Установки устройства** .

Функция **register** для подготовки запроса вызывает другие закрытые функции. После получения ответа вызывается завершение и показывается, была ли регистрация успешной.

Конечная точка запроса создается с помощью функции **getBaseAddress** . При построении используются параметры центра уведомлений *namespace* и *name* , предоставленные во время инициализации.

Функция **getSasToken** проверяет, является ли текущий сохраненный маркер допустимым. Если маркер недопустимый, функция вызывает **TokenUtility** для создания нового маркера и сохраняет его перед возвратом значения.

Наконец, **encodeToJson** преобразует соответствующие объекты модели в JSON для использования в качестве части запроса.

## <a name="invoke-the-notification-hubs-rest-api"></a>Вызов REST API Центров уведомлений

Последний шаг — обновление **AppDelegate** , чтобы использовать **NotificationRegistrationService** для регистрации в **NotificationHub**.

1. Откройте файл **AppDelegate.swift** и добавьте переменные уровня класса для хранения ссылки на **NoficiationRegistrationService** и стандартного **PushTemplate**:

   ```swift
   var registrationService : NotificationRegistrationService?
   let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
   ```

2. В том же файле обновите функцию **didRegisterForRemoteNotificationsWithDeviceToken** , чтобы инициализировать **NotificationRegistrationService** с исходными параметрами, а затем вызовите функцию **register** .

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

   Для простоты инструкции `print` в коде используются для обновления окна вывода с результатами работы функции **register** .

3. Создание и запуск приложения на физическом устройстве. В окне вывода должны появиться сообщение **Registered** (Зарегистрировано).

## <a name="test-the-solution"></a>Тестирование решения

На этом этапе приложение регистрируется в **NotificationHub** и может получать push-уведомления. В Xcode завершите работу отладчика и закройте приложение, если оно в данный момент выполняется. Затем убедитесь, что в поле **Установка устройства** отображаются ожидаемые сведения и приложение теперь может получать push-уведомления.

### <a name="verify-the-device-installation"></a>Проверка установки устройства

Теперь можно выполнить тот же запрос, что и ранее, используя **Postman** для [проверки маркера SAS](notification-hubs-ios-push-notifications-swift-apps-get-started.md#verify-the-sas-token). Если срок действия маркера SAS не истек, ответ должен содержать предоставленные сведения об установке, такие как шаблоны и теги.

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

Если срок действия предыдущего маркера SAS истек, можно добавить точку останова в строку 24 класса **TokenUtility** , чтобы получить новый маркер SAS и обновить заголовок **Authorization** новым значением.

### <a name="send-a-test-notification-azure-portal"></a>Отправка тестового уведомления (портал Azure)

Самый быстрый способ проверить, что теперь можно получать уведомления, — перейти в центр уведомлений на портале Azure:

1. На портале Azure перейдите на вкладку  **Обзор** в центре уведомлений.

2. Выберите команду **Test Send**(Проверить отправку), которая находится над сводкой **Главное** в верхнем левом углу окна портала:

   :::image type="content" source="media/ios-sdk-swift-rest/image2.png" alt-text="Тест отправки около сводки "Главное" для центров уведомлений":::

3. Выберите **Настраиваемый шаблон** из списка **Платформы** .

4. Введите **12345** для **Send to Tag Expression** (Отправить в выражение тега). Этот тег был ранее указан в установке.

5. При необходимости измените  **сообщение** в полезных данных JSON:

   :::image type="content" source="media/ios-sdk-swift-rest/image3.png" alt-text="Центры уведомлений — тестовая отправка":::

6. Выберите  **Отправить**. На портале должно быть указано, было ли уведомление успешно отправлено на устройство:

   :::image type="content" source="media/ios-sdk-swift-rest/image4.png" alt-text="Результат тестовой отправки":::

   Если приложение не выполняется на переднем плане, вы также увидите уведомление в  **Центре уведомлений** на своем устройстве. Если коснуться уведомления, откроется приложение и отобразится оповещение.

   :::image type="content" source="media/ios-sdk-swift-rest/image5.png" alt-text="Тестовое уведомление":::

### <a name="send-a-test-notification-mail-carrier"></a>Отправка тестового уведомления (почтой)

Уведомления можно отправлять через [REST API](/rest/api/notificationhubs/) с помощью **Postman**, что может оказаться более удобным способом тестирования.

1. Откройте новую вкладку в **Postman**.

2. Задайте в качестве метода запроса  **POST** и введите следующий адрес:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
   ```

3. Настройте заголовки запроса следующим образом:

   | Клавиши                           | Значение                          |
   | ----------------------------- | ------------------------------ |
   | Content-Type                  | application/json;charset=utf-8 |
   | Авторизация                 | \<sasToken\>                   |
   | ServiceBusNotification-Format | шаблон                       |
   | Теги                          | 12345                        |

4. Настройте текст  **BODY** запроса для использования **RAW-JSON (application.json)**  со следующими полезными данными JSON:

   ```json
   {
   "message" : "Hello from Postman!"
   }
   ```

5. Нажмите кнопку **Код**  в правом верхнем углу экрана под кнопкой  **Сохранить** . Запрос должен выглядеть следующим образом:

   ```xml
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

6. Нажмите кнопку  **Отправить** .

Вы должны получить код  **201 Created**  успешного выполнения и получить уведомление на клиентском устройстве.

## <a name="next-steps"></a>Дальнейшие действия

Теперь у вас есть базовое приложение Swift для iOS, подключенное к Центру уведомлений через [REST API Центров уведомлений](/rest/api/notificationhubs/), и вы можете отправлять и получать уведомления. Чтобы узнать больше, как отправлять уведомления на конкретные устройства, перейдите к следующему руководству:

- [Руководство. Отправка push-уведомлений на конкретные устройства](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Дополнительные сведения см. в следующих статьях:

- [Общие сведения о Центрах уведомлений Azure](notification-hubs-push-notification-overview.md)
- [Использование интерфейса REST центров уведомлений](/rest/api/notificationhubs/)
- [Пакет средств разработки Центров уведомлений для операций серверной части](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Пакет средств разработки для Центров уведомлений на GitHub](https://github.com/Azure/azure-notificationhubs)
- [Регистрация в серверной части приложения](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Управление регистрацией](notification-hubs-push-notification-registration-management.md)
- [Работа с тегами](notification-hubs-tags-segment-push-message.md)
- [Работа с пользовательскими шаблонами](notification-hubs-templates-cross-platform-push-messages.md)
- [Управление доступом к служебной шине с помощью подписанных URL-адресов](../service-bus-messaging/service-bus-sas.md)
- [Программное создание маркеров SAS](/rest/api/eventhub/generate-sas-token)
- [Безопасность в Apple: Common Crypto](https://developer.apple.com/security/)
- [Формат времени UNIX Epoch](https://en.wikipedia.org/wiki/Unix_time)
- [Код проверки подлинности сообщений с помощью хэш-функций](https://en.wikipedia.org/wiki/HMAC)
