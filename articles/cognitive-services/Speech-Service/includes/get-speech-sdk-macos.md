---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 82c45919892721d689bd90b7480158c4eea16c03
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374996"
---
При разработке для macOS доступно три пакета SDK для распознавания речи.

- Пакет SDK цели-C для распознавания речи доступен в виде пакета Кокоапод.
- Пакет SDK для .NET для распознавания речи можно использовать с **Xamarin. Mac** , так как он реализует .NET Standard 2,0
- Пакет SDK для распознавания речи Python доступен в виде модуля PyPI

> [!TIP]
> Дополнительные сведения об использовании речевого пакета "объектив-C" с помощью SWIFT см. в разделе <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Импорт цели-c в SWIFT <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

### <a name="system-requirements"></a>Требования к системе

- MacOS версии 10,13 или более поздней.

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        Пакет macOS Кокоапод доступен для загрузки и использования в интегрированной среде разработки (IDE) <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 <span class="docon docon-navigate-external x-hidden-focus"></span> (или более поздней версии)</a> . Сначала <a href="https://aka.ms/csspeech/macosbinary" target="_blank">Скачайте двоичный кокоапод <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Извлеките модуль Pod в том же каталоге, в котором он использовался, создайте *Podfile* и перечислите его `pod` как `target` .
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
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.13.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin. Mac предоставляет полный пакет SDK для macOS для разработчиков .NET, позволяющий создавать собственные приложения Mac на C#. Дополнительные сведения см. в разделе <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin. <span class="docon docon-navigate-external x-hidden-focus"></span> Mac </a>.
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

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">Краткий пример пакета SDK для macOS для распознавания речи — исходный код на языке C<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">исходный код руководства по пакету SDK для macOS для распознавания речи<span class="docon docon-navigate-external x-hidden-focus"></span></a>