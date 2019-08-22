---
title: Краткое руководство. Создание приложения iOS, которое запускает иммерсивное средство чтения (Swift)
titleSuffix: Azure Cognitive Services
description: В рамках этого краткого руководства вы создадите с нуля приложение iOS и добавите функции иммерсивного средства чтения.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 64fd7508244f2123cc10ee96ec4f805050aedfaa
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899532"
---
# <a name="quickstart-create-an-ios-app-that-launches-the-immersive-reader-swift"></a>Краткое руководство. Создание приложения iOS, которое запускает иммерсивное средство чтения (Swift)

[Иммерсивное средство чтения](https://www.onenote.com/learningtools) — это включительно разработанное решение, в котором реализованы проверенные методы, улучшающие понимание при чтении.

В рамках этого краткого руководства вы создадите с нуля приложение iOS и интегрируете иммерсивное средство чтения с помощью пакета SDK иммерсивного средства чтения. Полностью рабочий пример этого краткого руководства доступен [здесь](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/quickstart-swift).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Ресурс "Иммерсивное средство чтения", настроенный для проверки подлинности Azure Active Directory (Azure AD). Инструкции по настройке см. [здесь](./azure-active-directory-authentication.md). Некоторые из этих созданных значений потребуются вам при настройке свойств примера проекта. Сохраните результаты своего сеанса в текстовом файле для использования в будущем.

## <a name="create-an-xcode-project"></a>Создание проекта Xcode

Создайте проект в Xcode.

![Новый проект](./media/ios/xcode-create-project.png)

Выберите **Приложение одного представления**.

![Новое приложение одного представления](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Получение пакета SDK CocoaPod
Самый простой способ использовать пакет SDK для Иммерсивного средства чтения — через CocoaPods. Чтобы установить его с помощью Cocoapods, выполните следующее.
1. [Установите CocoaPods](http://guides.cocoapods.org/using/getting-started.html) по инструкциям из руководства по началу работы.
2. Создайте Podfile, запустив `pod init` в корневом каталоге проекта Xcode.
3.  Добавьте CocoaPod в Podfile, добавив `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`. Ваш Podfile должен выглядеть, как представлено ниже, только вместо quickstart-swift будет стоять имя конкретного целевого объекта:
 ```ruby
  platform :ios, '9.0'

  target 'quickstart-swift' do
  use_frameworks!
  # Pods for quickstart-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. В окне терминала в каталоге проекта Xcode запустите команду `pod install`, чтобы установить pod пакета SDK для иммерсивного средства чтения
5. Добавьте `import immersive_reader_sdk` ко всем файлам, которые должны ссылаться на этот пакет SDK.
6. Убедитесь, что проект открыт, открыв файл `.xcworkspace`, а не `.xcodeproj`.

## <a name="acquire-an-azure-ad-authentication-token"></a>Получение маркера проверки подлинности Azure AD

Для этой части потребуются некоторые значения из описанного выше предварительного требования конфигурации проверки подлинности Azure AD. Вернитесь к текстовому файлу, который вы сохранили в этом сеансе.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

В главной папке проекта, которая содержит файл ViewController.swift, создайте файл для класса Swift с именем Constants.swift. Замените этот класс следующим кодом, добавив в него нужные реальные значения. Сохраните этот файл как локальный, существующий только на вашем компьютере, и убедитесь, что этот файл не передан в систему управления версиями, так как он содержит секреты, которые не следует публиковать. Мы рекомендуем не сохранять секреты в коде приложения. Вместо этого лучше использовать внутреннюю службу для получения токена, что позволяет хранить секреты вне приложения и вне устройства. Конечная точка внутреннего-API должна быть защищена какой-либо формой аутентификации (например, [OAuth](https://oauth.net/2/)), чтобы предотвратить получение неавторизованными пользователями токенов для использования в службе иммерсивного средства чтения и выставлении счетов. Эти действия не входят в данное руководство.

[!code-swift[Constants](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/Constants.swift)]

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Настройка приложения для запуска без раскадровки

Откройте AppDelegate.swift и замените его содержимое следующим кодом.

[!code-swift[AppDelegate](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/AppDelegate.swift)]

## <a name="create-the-launch-view-controller-and-add-sample-content"></a>Создание контроллера представления запуска и добавление примера содержимого

Переименуйте ViewController.swift в LaunchViewController.swift и замените его содержимое следующим кодом.

[!code-swift[LaunchViewController](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/LaunchViewController.swift)]

## <a name="build-and-run-the-app"></a>Создание и запуск приложения

Настройте схему архивации в Xcode, выбрав симулятор или цель устройства.
![Схема архива](./media/ios/xcode-archive-scheme.png)<br/>
![Выбор цели](./media/ios/xcode-select-target.png)

В Xcode нажмите сочетание клавиш CTRL+R или нажмите кнопку "Воспроизведение", чтобы запустить проект. Приложение должно запуститься в указанном симуляторе или устройстве.

В приложении вы должны увидеть:

![Пример приложения](./media/ios/sample-app-ipad.png)

При нажатии кнопки "иммерсивное средство чтения" появится запущенное иммерсивное средство чтения с содержимым приложения.

![Иммерсивное средство чтения](./media/ios/immersive-reader-ipad.png)

## <a name="next-steps"></a>Дополнительная информация

* Изучите раздел о[пакете SDK Иммерсивного средства чтения для iOS](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS) и [Справочник о пакете SDK Иммерсивного средства чтения для iOS](./ios-reference.md)
