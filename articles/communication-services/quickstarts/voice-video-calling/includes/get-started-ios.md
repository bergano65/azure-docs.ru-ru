---
title: Краткое руководство. Добавление функции вызова в приложение iOS с помощью Служб коммуникации Azure
description: Из этого краткого руководства вы узнаете, как использовать клиентскую библиотеку Служб коммуникации Azure для реализации вызовов на iOS.
author: matthewrobertson
ms.author: marobert
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 63b74675a9b0d3480c90c7414e82658705796e7c
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92438926"
---
Из этого краткого руководства вы узнаете, как начать вызов с помощью клиентской библиотеки Служб коммуникации Azure для реализации вызовов для iOS.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется:

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно. 
- компьютер Mac с [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), а также действительный сертификат разработчика, установленный в цепочку ключей;
- Развернутый ресурс Служб коммуникации. [Создайте ресурс Служб коммуникации.](../../create-communication-resource.md)
- [Маркер доступа пользователя](../../access-tokens.md) для Службы коммуникации Azure.

## <a name="setting-up"></a>Настройка

### <a name="creating-the-xcode-project"></a>Создание проекта Xcode

В Xcode создайте новый проект iOS и выберите шаблон **Single View App** (Приложение с одним представлением). В этом руководстве используется [платформа SwiftUI](https://developer.apple.com/xcode/swiftui/), поэтому для параметра **Language** (Язык) нужно задать значение **Swift** , а для параметра **User Interface** (Пользовательский интерфейс) — значение **SwiftUI** . В рамках этого краткого руководства вы не будете создавать тесты. Вы можете снять флажок **Include Tests** (Включить тесты).

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Снимок экрана с окном New Project (Новый проект) в Xcode.":::

### <a name="install-the-package"></a>Установка пакета

Добавьте к своему проекту клиентскую библиотеку Служб коммуникации Azure для вызовов и ее зависимости (AzureCore.framework и AzureCommunication.framework).

> [!NOTE]
> В выпуске пакета SDK AzureCommunicationCalling вы найдете скрипт bash `BuildAzurePackages.sh`. При запуске (`sh ./BuildAzurePackages.sh`) скрипт предоставит вам путь к созданным пакетам платформы, которые необходимо импортировать в пример приложения на следующем шаге. Обратите внимание, что вам потребуется настроить средства командной строки Xcode, если вы не сделали этого до запуска скрипта: Запустите Xcode и выберите Preferences -> Locations (Настройки -> Расположения). Выберите свою версию Xcode для средств командной строки. **Скрипт BuildAzurePackages.sh работает только с Xcode 11.5 и более поздних версий**

1. [Скачайте](https://github.com/Azure/Communication/releases) клиентскую библиотеку Служб коммуникации Azure для реализации вызовов для iOS.
2. В Xcode щелкните файл проекта и выберите целевой объект сборки, чтобы открыть редактор параметров проекта.
3. На вкладке **General** (Общие) прокрутите к разделу **Frameworks, Libraries, and Embedded Content** (Платформы, библиотеки и встроенное содержимое) и щелкните значок **+** .
4. В левом нижнем углу диалогового окна выберите в раскрывающемся списке элемент **Add Files** (Добавить файлы) и перейдите к каталогу **AzureCommunicationCalling.framework** разархивированного пакета клиентской библиотеки.
    1. Повторите последний шаг, чтобы добавить **AzureCore.framework** и **AzureCommunication.framework** .
5. Откройте вкладку **Build Settings** (Параметры сборки) в редакторе параметров проекта и прокрутите к разделу **Search Paths** (Пути поиска). Добавьте новую запись **Framework Search Paths** (Пути поиска платформы) для каталога, содержащего **AzureCommunicationCalling.framework** .
    1. Добавьте еще одну запись путей поиска платформы, указывающую на папку с зависимостями.

:::image type="content" source="../media/ios/xcode-framework-search-paths.png" alt-text="Снимок экрана с окном New Project (Новый проект) в Xcode.":::

### <a name="request-access-to-the-microphone"></a>Запрос доступа к микрофону

Чтобы получить доступ к микрофону устройства, вам необходимо указать ключ `NSMicrophoneUsageDescription` в списке свойств сведений приложения. Задайте связанное значение для строки `string`, которая будет включена в диалоговое окно, отображаемое системой при запрашивании доступа у пользователя.

В дереве проекта щелкните правой кнопкой мыши запись `Info.plist` и выберите **Open As** > **Source Code** (Открыть как > Исходный код). Добавьте в раздел верхнего уровня `<dict>` следующие строки, а затем сохраните файл.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Настройка платформы приложения

Откройте файл **ContentView.swift** проекта и добавьте объявление `import` в начало файла, чтобы импортировать `AzureCommunicationCalling library`. Кроме того, импортируйте `AVFoundation`, чтобы мы могли реализовать в коде запрос на доступ к аудио.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Замените реализацию структуры `ContentView` простыми элементами управления пользовательского интерфейса, которые позволяют начать и завершить вызов. В рамках этого краткого руководства к таким элементам управления мы присоединим бизнес-логику.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: ACSCallClient?
    @State var callAgent: ACSCallAgent?
    @State var call: ACSCall?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>Объектная модель

Следующие классы и интерфейсы реализуют некоторые основные функции клиентской библиотеки Служб коммуникации Azure для вызовов:

| Имя                                  | Описание                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | CallClient — это основная точка входа в клиентскую библиотеку для вызовов.|
| ACSCallAgent | CallAgent используется для инициирования вызовов и управления ими. |
| CommunicationUserCredential | CommunicationUserCredential используется в качестве учетных данных маркера для создания экземпляра CallAgent.| 
| CommunicationIdentifier | CommunicationIdentifier используется для представления идентификатора пользователя, который может иметь один из следующих типов: CommunicationUser, PhoneNumber, CallingApplication. |

## <a name="authenticate-the-client"></a>Аутентификация клиента

Инициализируйте экземпляр `CallAgent` с помощью маркера доступа пользователя, что позволит нам выполнять и принимать вызовы. Добавьте следующий код в обратный вызов `onAppear` в **ContentView.swift** :

```swift
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = ACSCallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

Вам необходимо заменить `<USER ACCESS TOKEN>` допустимым маркером доступа пользователя для вашего ресурса. Если у вас еще нет доступного маркера, см. документацию по [маркеру доступа пользователя](../../access-tokens.md).

## <a name="start-a-call"></a>Инициирование вызова

Метод `startCall` задан в качестве действия, которое будет выполняться при нажатии кнопки *Начать вызов* . Измените реализацию, чтобы вызов начинался с помощью `ASACallAgent`:

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUser(identifier: self.callee)]
            self.call = self.callAgent?.call(callees, options: ACSStartCallOptions())
        }
    }
}
```

Вы также можете использовать свойства в `ACSStartCallOptions`, чтобы задать первоначальные параметры для вызова (т. е. можно начать вызов с отключенным микрофоном).

## <a name="end-a-call"></a>Завершение вызова

Реализуйте метод `endCall`, чтобы завершать текущий вызов при нажатии кнопки *Завершить вызов* .

```swift
func endCall()
{    
    self.call!.hangup(ACSHangupOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>Выполнение кода

Вы можете создать и запустить свое приложение в симуляторе iOS, выбрав **Product** > **Run** (Продукт > Выполнить) или с помощью клавиш (&#8984;-R).

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="Снимок экрана с окном New Project (Новый проект) в Xcode.":::

Вы можете выполнить исходящий VOIP-вызов, указав идентификатор пользователя в текстовом поле и нажав кнопку **Начать вызов** . При вызове `8:echo123` вы будете подключены к эхо-боту, что позволит начать работу и проверить работоспособность ваших аудиоустройств. 

> [!NOTE]
> При первом вызове в системе отобразится запрос на получение доступа к микрофону. В приложении в рабочей среде используйте API `AVAudioSession` для [проверки состояния разрешения](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) и корректно обновите поведение приложения, если разрешение не предоставлено.

## <a name="sample-code"></a>Пример кода

Пример приложения можно скачать в репозитории [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling).
