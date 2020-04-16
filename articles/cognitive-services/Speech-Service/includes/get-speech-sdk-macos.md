---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 57de7f1e7c37fec66cda666d3f144e52849a026f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399971"
---
При разработке для macOS, Есть три речи SDKs доступны.

- Объектив-C Речи SDK доступна на родине в качестве пакета CocoaPod
- SDK речи .NET может быть использован с **Xamarin.Mac,** как он реализует .NET Стандарт 2.0
- Python Speech SDK доступен в виде модуля PyPI

> [!TIP]
> Для получения подробной информации, используя объектив-C речи SDK с Swift, см <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Импортирование объективных-C в Swift <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

### <a name="system-requirements"></a>Требования к системе

- MacOS версия 10.13 или позже

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        Пакет macOS CocoaPod доступен для скачивания и использования с <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode <span class="docon docon-navigate-external x-hidden-focus"></span> 9.4.1 (или позже)</a> интегрированной средой разработки (IDE). Во-первых, <a href="https://aka.ms/csspeech/macosbinary" target="_blank">скачать двоичный CocoaPod <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. Извлеките стручок в том же каталоге для его `pod` предполагаемого использования, создайте *Podfile* и перечислите как . `target`
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

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.11.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin. Mac предоставляет полный пакет SDK для macOS для разработчиков .NET, позволяющий создавать собственные приложения Mac на C#. Для получения дополнительной информации <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">см. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>Дополнительные ресурсы

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS Речь SDK быстрый запуск Объективный-C исходный код<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS речи SDK быстрый запуск Swift исходный код<span class="docon docon-navigate-external x-hidden-focus"></span></a>