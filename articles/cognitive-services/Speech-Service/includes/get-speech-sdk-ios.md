---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 7ce193c2c2f5e10a27550da68a4c2d2fdcd1db7f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399955"
---
:::row:::
    :::column span="3":::
        При разработке для iOS доступны два Бортовых SDK. Объективная речь SDK доступна на родном уровне в виде пакета iOS CocoaPod. Кроме того, .NET Speech SDK может быть использован с Xamarin.iOS, поскольку он реализует .NET Standard 2.0.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Для получения подробной информации, используя объектив-C речи SDK с Swift, см <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Импортирование объективных-C в Swift <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

### <a name="system-requirements"></a>Требования к системе

- MacOS версия 10.3 или позже
- Целевой iOS 9.3 или позже

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        Пакет iOS CocoaPod доступен для скачивания и использования с <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">xcode <span class="docon docon-navigate-external x-hidden-focus"></span> 9.4.1 (или позже)</a> интегрированной средой разработки (IDE). Во-первых, <a href="https://aka.ms/csspeech/iosbinary" target="_blank">скачать двоичный CocoaPod <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. Извлеките стручок в том же каталоге для его `pod` предполагаемого использования, создайте *Podfile* и перечислите как . `target`
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.iOS предоставляет полный пакет SDK для iOS для разработчиков .NET. Создавайте собственные приложения iOS с помощью C# и F# в Visual Studio. Для получения дополнительной информации <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">см. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;❤️ &nbsp;        <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>Дополнительные ресурсы

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">IOS Речь SDK быстрый запуск Объективный-C исходный код<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">IOS Речь SDK быстрый запуск Swift исходный код<span class="docon docon-navigate-external x-hidden-focus"></span></a>