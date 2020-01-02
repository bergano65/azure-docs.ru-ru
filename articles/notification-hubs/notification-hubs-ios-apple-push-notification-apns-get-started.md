---
title: Отправка push-уведомлений в приложения iOS с помощью Центров уведомлений Azure | Документация Майкрософт
description: Из этого руководства вы узнаете, как отправлять push-уведомления в приложения iOS с помощью центров уведомлений Azure.
services: notification-hubs
documentationcenter: ios
keywords: push-уведомление, push-уведомления, push-уведомления node.js, push-уведомления ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 032ca8d4ecbcf1fc7f3c22cbe5a0ee934fc5e17c
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407170"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Руководство по отправке push-уведомлений в приложения iOS с помощью Центров уведомлений Azure

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

При работе с этим руководством вы отправите push-уведомления в приложения iOS с помощью Центров уведомлений Azure. Вы создадите пустое приложение iOS, получающее push-уведомления с помощью [Службы push-уведомлений Apple (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

В этом руководстве вы выполните следующие задачи:

> [!div class="checklist"]
> * создание файла запроса на подпись сертификата;
> * регистрация приложения для работы с push-уведомлениями;
> * Создание профиля подготовки для приложения
> * настройка push-уведомлений iOS в центре уведомлений;
> * подключение приложения iOS к центрам уведомлений;
> * Отправка тестовых push-уведомлений
> * проверка получения уведомлений приложением.

Полный код для этого руководства можно найти на [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/master/Samples).

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством вам потребуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, вы можете создать [бесплатную учетную запись Azure](https://azure.microsoft.com/free).
* [Платформа обмена сообщениями Microsoft Azure]
* последняя версия [Xcode]
* устройство с iOS 10 (или более поздней версии);
* [программе для разработчиков на платформе Apple](https://developer.apple.com/programs/) .
  
  > [!NOTE]
  > В соответствии с требованиями к настройкам push-уведомлений необходимо развернуть push-уведомления и протестировать их на физическом устройстве под управлением iOS (iPhone или iPad), а не в эмуляторе iOS.
  
Изучение этого руководства является необходимым условием для работы со всеми другими руководствами, посвященными Центрам уведомлений для приложений iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Подключение приложения iOS к центрам уведомлений

1. В XCode создайте новый проект iOS и выберите шаблон **Single View Application** (Приложение с одним представлением).

    ![Xcode — приложение с одним представлением][8]

2. При настройке параметров нового проекта используйте те же **имя продукта** и **идентификатор организации**, которые вы использовали при указании идентификатора пакета на портале разработчиков Apple.

3. В навигаторе проекта выберите имя проекта в разделе **Цели**, а затем вкладку **Signing & Capabilities** (Подписывание и возможности). Обязательно выберите соответствующую **команду** для учетной записи разработчика Apple. Среда XCode должна автоматически извлечь профиль подготовки, который вы создали ранее, используя ваш идентификатор пакета.

    Если новый профиль подготовки, созданный в Xcode, не отображается, обновите профили для идентификатора подписи. В строке меню щелкните **Xcode** выберите **Preferences** (Настройки), откройте вкладку **Account** (Учетная запись), нажмите кнопку **View Details** (Просмотреть сведения), щелкните свой идентификатор подписи, а затем нажмите кнопку Refresh (Обновить) в нижнем правом углу.

    ![Xcode — профиль подготовки][9]

4. На вкладке **Signing & Capabilities** (Подписывание и возможности) выберите **+ Capability** (+ Возможность).  Дважды щелкните **Push-уведомления**, чтобы включить их.

    ![Xcode — возможности push-уведомлений][12]

5. Добавьте модули пакета SDK для Центров уведомлений Azure.

   Вы можете интегрировать пакет SDK для Центров уведомлений Azure в свое приложение с помощью [Cocoapods](https://cocoapods.org) или добавляя двоичные файлы в свой проект вручную.

   - Интеграция с помощью Cocoapods

     Добавьте следующие зависимости в `podfile`, чтобы включить пакет SDK для Центров уведомлений Azure в свое приложение.

     ```ruby
     pod 'AzureNotificationHubs-iOS'
     ```

     Выполните `pod install`, чтобы установить новый модуль pod и открыть `.xcworkspace`.

     > [!NOTE]
     > Если вы видите ошибку типа **[!] Unable to find a specification for AzureNotificationHubs-iOS** (Не удается найти спецификацию для AzureNotificationHubs-iOS) во время работы `pod install`, выполните `pod repo update`, чтобы получить последние pod из репозитория Cocoapods, а затем выполните `pod install`.

   - Интеграция с помощью Carthage

     Добавьте следующие зависимости в `Cartfile`, чтобы включить пакет SDK для Центров уведомлений Azure в свое приложение.

     ```ruby
     github "Azure/azure-notificationhubs-ios"
     ```

     Затем обновите и создайте зависимости:

     ```shell
     $ carthage update
     ```

     Дополнительные сведения об использовании Carthage см. в [репозитории GitHub](https://github.com/Carthage/Carthage).

   - Интеграция путем копирования двоичных файлов в проект

     1. Скачайте и распакуйте платформу [пакета SDK для Центров уведомлений Azure](https://github.com/Azure/azure-notificationhubs-ios/releases), предоставляемую в виде ZIP-файла.

     2. В XCode щелкните проект правой кнопкой мыши и выберите параметр **Add Files to** (Добавить файлы в), чтобы добавить папку **WindowsAzureMessaging.framework** в проект XCode. Нажмите кнопку **Options** (Параметры), установите флажок **Copy items if needed** (Копировать элементы при необходимости) и нажмите кнопку **Add** (Добавить).

        ![Распаковка пакета SDK Azure][10]

6. Добавьте новый файл заголовка в проект с именем **Constants.h**. Для этого щелкните имя проекта правой кнопкой мыши и выберите **New File...** (Создать файл...). Затем выберите **Файл заголовка**. Этот файл содержит константы для центра уведомлений. Затем нажмите кнопку **Далее**. Назовите файл **Constants.h**.

7. Добавьте в него указанный ниже код:

    ```objc
    #ifndef Constants_h
    #define Constants_h

    extern NSString* const NHInfoConnectionString;
    extern NSString* const NHInfoHubName;
    extern NSString* const NHUserDefaultTags;

    #endif /* Constants_h */
    ```

8. Добавьте файл реализации для Constants.h. Для этого щелкните имя проекта правой кнопкой мыши и выберите **New File...** (Создать файл...). Выберите **Objective-C File** (Файл Objective-C), а затем нажмите кнопку **Далее**. Назовите файл **Constants.m**.

    ![Добавление M-файла](media/notification-hubs-ios-get-started/new-file-objc.png)

9. Откройте файл **Constants.m** и замените его содержимое следующим кодом. Замените буквенные заполнители строк `NotificationHubConnectionString` и `NotificationHubConnectionString` именем центра и значением **DefaultListenSharedAccessSignature**, полученным ранее с портала:

    ```objc
    #import <Foundation/Foundation.h>
    #import "Constants.h"

    NSString* const NHInfoConnectionString = @"NotificationHubConnectionString";
    NSString* const NHInfoHubName = @"NotificationHubName";
    NSString* const NHUserDefaultTags = @"notification_tags";
    ```

10. Откройте файл проекта **AppDelegate.h** и замените его содержимое следующим кодом:

    ```objc
    #import <UIKit/UIKit.h>
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h> 

    @interface AppDelegate : UIResponder <UIApplicationDelegate,UNUserNotificationCenterDelegate>

    @property (strong, nonatomic) UIWindow *window;

    - (void)handleRegister;
    - (void)handleUnregister;

    @end

    ```

11. В файле проекта **AppDelegate.m** добавьте следующие операторы `import`:

    ```objc
    #import "Constants.h"
    #import "NotificationDetailViewController.h"
    ```

12. Кроме того, добавьте в файле **AppDelegate.m** следующую строку кода в метод `didFinishLaunchingWithOptions` с учетом вашей версии iOS. Этот код регистрирует маркер вашего устройства в APNs.

    ```objc
    [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
    ```

13. В том же файле **AppDelegate.m** замените весь код после `didFinishLaunchingWithOptions` следующим:

    ```objc
    // Tells the app that a remote notification arrived that indicates there is data to be fetched.

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))completionHandler {
        NSLog(@"Received remote (silent) notification");
        [self logNotificationDetails:userInfo];

        //
        // Let the system know the silent notification has been processed.
        //
        completionHandler(UIBackgroundFetchResultNoData);
    }

    // Tells the delegate that the app successfully registered with Apple Push Notification service (APNs).

    - (void) application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
        NSMutableSet *tags = [[NSMutableSet alloc] init];

        // Load and parse stored tags
        NSString *unparsedTags = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
        if (unparsedTags.length > 0) {
            NSArray *tagsArray = [unparsedTags componentsSeparatedByString: @","];
            [tags addObjectsFromArray:tagsArray];
        }

        // Register the device with the Notification Hub.
        // If the device has not already been registered, this will create the registration.
        // If the device has already been registered, this will update the existing registration.
        //
        SBNotificationHub* hub = [self getNotificationHub];
        [hub registerNativeWithDeviceToken:deviceToken tags:tags completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            } else {
                [self showAlert:@"Registered" withTitle:@"Registration Status"];
            }
        }];
    }

    // UNUserNotificationCenterDelegate methods
    //
    // Asks the delegate how to handle a notification that arrived while the app was running in the  foreground.

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler {
        NSLog(@"Received notification while the application is in the foreground");

        // The system calls this delegate method when the app is in the foreground. This allows the app to handle the notification
        // itself (and potentially modify the default system behavior).

        // Handle the notification by displaying custom UI.
        //
        [self showNotification:notification.request.content.userInfo];

        // Use 'options' to specify which default behaviors to enable.
        // - UNAuthorizationOptionBadge: Apply the notification's badge value to the app’s icon.
        // - UNAuthorizationOptionSound: Play the sound associated with the notification.
        // - UNAuthorizationOptionAlert: Display the alert using the content provided by the notification.
        //
        // In this case, do not pass UNAuthorizationOptionAlert because the notification was handled by the app.
        //
        completionHandler(UNAuthorizationOptionBadge | UNAuthorizationOptionSound);
    }

    // Asks the delegate to process the user's response to a delivered notification.
    //

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)(void))completionHandler {
        NSLog(@"Received notification while the application is in the background");

        // The system calls this delegate method when the user taps or responds to the system notification.

        // Handle the notification response by displaying custom UI
        //
        [self showNotification:response.notification.request.content.userInfo];

        // Let the system know the response has been processed.
        //
        completionHandler();
    }

    // App logic and helpers

    - (SBNotificationHub *)getNotificationHub {
        NSString *hubName = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoHubName];
        NSString *connectionString = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoConnectionString];

        return [[SBNotificationHub alloc] initWithConnectionString:connectionString notificationHubPath:hubName];
    }

    - (void)handleRegister {
        UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];

        UNAuthorizationOptions options =  UNAuthorizationOptionAlert | UNAuthorizationOptionSound | UNAuthorizationOptionBadge;
        [center requestAuthorizationWithOptions:(options) completionHandler:^(BOOL granted, NSError * _Nullable error) {
            if (error != nil) {
                NSLog(@"Error requesting for authorization: %@", error);
            }
        }];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    }

    - (void)handleUnregister {
        //
        // Unregister the device with the Notification Hub.
        //
        SBNotificationHub *hub = [self getNotificationHub];
        [hub unregisterNativeWithCompletion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error unregistering for push: %@", error);
            } else {
                [self showAlert:@"Unregistered" withTitle:@"Registration Status"];
            }
        }];
    }

    - (void)logNotificationDetails:(NSDictionary *)userInfo {
        if (userInfo != nil) {
            UIApplicationState state = [UIApplication sharedApplication].applicationState;
            BOOL background = state != UIApplicationStateActive;
            NSLog(@"Received %@notification: \n%@", background ? @"(background) " : @"", userInfo);
        }
    }

    - (void)showAlert:(NSString *)message withTitle:(NSString *)title {
        if (title == nil) {
            title = @"Alert";
        }
        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:message preferredStyle:UIAlertControllerStyleAlert];
        [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil]];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }

    - (void)showNotification:(NSDictionary *)userInfo {
        [self logNotificationDetails:userInfo];

        NotificationDetailViewController *notificationDetail = [[NotificationDetailViewController alloc] initWithUserInfo:userInfo];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:notificationDetail animated:YES completion:nil];
    }

    @end
    ```

    Этот код подключается к центру уведомлений, используя сведения о соединении, указанные в проекте **Constants.h**. Затем он передает маркер устройства в центр уведомлений, чтобы центр мог отправлять уведомления.

### <a name="notificationdetailviewcontroller"></a>NotificationDetailViewController

1. Аналогично предыдущим инструкциям добавьте другой файл заголовка **NotificationDetailViewController.h**. Замените содержимое нового файла приведенным ниже кодом:

    ```objc
    #import <UIKit/UIKit.h>

    NS_ASSUME_NONNULL_BEGIN

    @interface NotificationDetailViewController : UIViewController

    @property (strong, nonatomic) IBOutlet UILabel *titleLabel;
    @property (strong, nonatomic) IBOutlet UILabel *bodyLabel;
    @property (strong, nonatomic) IBOutlet UIButton *dismissButton;

    @property (strong, nonatomic) NSDictionary *userInfo;

    - (id)initWithUserInfo:(NSDictionary *)userInfo;

    @end

    NS_ASSUME_NONNULL_END
    ```

2. Добавьте файл реализации **NotificationDetailViewController.m**. Замените содержимое файла следующим кодом, который реализует методы `UIViewController`:

    ```objc
    #import "NotificationDetailViewController.h"

    @interface NotificationDetailViewController ()

    @end

    @implementation NotificationDetailViewController

    - (id)initWithUserInfo:(NSDictionary *)userInfo {
        self = [super initWithNibName:@"NotificationDetail" bundle:nil];
        if (self) {
            _userInfo = userInfo;
        }
        return self;
    }

    - (void)viewDidLayoutSubviews {
        [self.titleLabel sizeToFit];
        [self.bodyLabel sizeToFit];
    }

    - (void)viewDidLoad {
        [super viewDidLoad];

        NSString *title = nil;
        NSString *body = nil;

        NSDictionary *aps = [_userInfo valueForKey:@"aps"];
        NSObject *alertObject = [aps valueForKey:@"alert"];
        if (alertObject != nil) {
            if ([alertObject isKindOfClass:[NSDictionary class]]) {
                NSDictionary *alertDict = (NSDictionary *)alertObject;
                title = [alertDict valueForKey:@"title"];
                body = [alertObject valueForKey:@"body"];
            } else if ([alertObject isKindOfClass:[NSString class]]) {
                body = (NSString *)alertObject;
            } else {
                NSLog(@"Unable to parse notification content. Unexpected format: %@", alertObject);
            }
        }

        if (title == nil) {
            title = @"<unset>";
        }

        if (body == nil) {
            body = @"<unset>";
        }

        self.titleLabel.text = title;
        self.bodyLabel.text = body;
    }

    - (IBAction)handleDismiss:(id)sender {
        [self dismissViewControllerAnimated:YES completion:nil];
    }

    @end
    ```

### <a name="viewcontroller"></a>ViewController

1. В файле проекта **ViewController.h** добавьте следующие операторы `import`:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>
    ```

2. Кроме того, в **ViewController.h** добавьте следующие объявления свойств после объявления `@interface`:

    ```objc
    @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
    @property (strong, nonatomic) IBOutlet UIButton *registerButton;
    @property (strong, nonatomic) IBOutlet UIButton *unregisterButton;
    ```

3. Замените содержимое файла реализации проекта **ViewController.m** следующим кодом:

    ```objc
    #import "ViewController.h"
    #import "Constants.h"
    #import "AppDelegate.h"

    @interface ViewController ()

    @end

    @implementation ViewController

    // UIViewController methods

    - (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
        // Simple method to dismiss keyboard when user taps outside of the UITextField.
        [self.view endEditing:YES];
    }

    - (void)viewDidLoad {
        [super viewDidLoad];

        // Load raw tags text from storage and initialize the text field
        self.tagsTextField.text = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
    }

    - (IBAction)handleRegister:(id)sender {
        // Save raw tags text in storage
        [[NSUserDefaults standardUserDefaults] setValue:self.tagsTextField.text forKey:NHUserDefaultTags];

    // Delegate processing the register action to the app delegate.
    [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleRegister)];
    }

    - (IBAction)handleUnregister:(id)sender {
        [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleUnregister)];
    }

    @end
    ```

4. Выполните сборку приложения и запустите его на устройстве, чтобы убедиться в отсутствии сбоев.

## <a name="send-test-push-notifications"></a>Отправка тестовых push-уведомлений

Можно проверить, поступают ли в приложение уведомления, с помощью параметра *Тестовая отправка* на [портал Azure]. Этот параметр позволяет отправить на устройство тестовое push-уведомление.

![Портал Azure — тестовая отправка][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="verify-that-your-app-receives-push-notifications"></a>Проверка получения push-уведомлений приложением

Для тестирования push-уведомлений в iOS необходимо развернуть приложение на физическом устройстве под управлением iOS. Отправка push-уведомлений Apple через эмулятор iOS невозможна.

1. Запустите приложение и убедитесь, что оно успешно зарегистрировано, а затем нажмите кнопку **ОК**.

    ![Проверка регистрации push-уведомления приложения iOS][33]

2. Отправьте тестовое push-уведомление с [портал Azure], как описано в предыдущем разделе.

3. Push-уведомление будет отправлено на все устройства, зарегистрированные для получения уведомлений от того или иного центра уведомлений.

    ![Проверка получения push-уведомления приложения iOS][35]

## <a name="next-steps"></a>Дополнительная информация

В этом простом примере мы отправили push-уведомления на все зарегистрированные устройства iOS. Сведения о том, как отправлять push-уведомления на конкретные устройства iOS, см. в следующем руководстве:

> [!div class="nextstepaction"]
>[Руководство по отправке push-уведомлений на конкретные устройства Android с помощью Центров уведомлений Azure и Google Cloud Messaging](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png

<!-- URLs. -->
[Платформа обмена сообщениями Microsoft Azure]: https://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[портал Azure]: https://portal.azure.com
