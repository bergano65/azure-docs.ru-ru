---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: bd7db1dd5d5fd6e19584e66a9af1523567d593bf
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631141"
---
## <a name="prerequisites"></a>Предварительные требования

В рамках этого руководства вам потребуются:

* Прочесть статью [Описание службы "Пространственные привязки Azure"](../articles/spatial-anchors/overview.md).
* Выполнить одно из [5-минутных руководств](../articles/spatial-anchors/index.yml).
* Базовые знания C# и Unity.
* Базовые знания <a href="https://developers.google.com/ar/discover/" target="_blank">ARCore</a>, если вы хотите использовать Android, или <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a>, если вы хотите использовать iOS.
* Компьютер Windows с установленным решением <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 или более поздней версии</a> с рабочей нагрузкой **ASP.NET и разработка веб-приложений**.
* [Пакет SDK .NET Core 2.2](https://dotnet.microsoft.com/download).
* Одно или несколько устройств (iOS или Android) для развертывания и запуска приложения.
  * Если вы используете Android, необходимо иметь:
    * Компьютер Windows с установленными <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3</a> или более поздней версии, <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3</a> или более поздней версии и <a href="https://git-scm.com/download/win" target="_blank">Git для Windows</a>.
    * Устройство Android с включенным <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">режимом разработчика</a> и поддержкой <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore</a>.
  * Если вы используете iOS, необходимо иметь:
    * Компьютер с macOS с установленными <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a> или более поздних версий, <a href="https://cocoapods.org" target="_blank">CocoaPods</a> и <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3</a> или более поздних версий.
    * Устройство для разработки на iOS, <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">совместимое с ARKit</a>.
    * Система Git, установленная с помощью Homebrew. Введите в одну строку терминала такую команду: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` Затем выполните команду `brew install git`.


