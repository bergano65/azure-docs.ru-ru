---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0e81a9b6a91d2a9ed1af922dbbc53720022ff598
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657861"
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
    * Компьютер Windows с установленными <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3</a> или более поздней версии, <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1</a> или более поздней версии и <a href="https://git-scm.com/download/win" target="_blank">Git для Windows</a>.
    * Устройство Android с включенным <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">режимом разработчика</a> и поддержкой <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore</a>.
  * Если вы используете iOS, необходимо иметь:
    * Компьютер с macOS с установленными <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a> или более поздних версий, <a href="https://cocoapods.org" target="_blank">CocoaPods</a> и <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1</a> или более поздних версий.
    * Устройство для разработки на iOS, <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">совместимое с ARKit</a>.
    * Система Git, установленная с помощью Homebrew. Введите в одну строку терминала такую команду: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` Затем выполните команду `brew install git`.


