---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 33cc9b52c4b687ca8147867b99dc70e8d71a9223
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88944464"
---
:::row:::
    :::column span="3":::
        Пакет SDK для распознавания речи поддерживает Windows 10 и Windows Server 2016 или более поздние версии. Более ранние версии **не** поддерживаются официально. Можно использовать части пакета SDK для распознавания речи в более ранних версиях Windows, хотя это не рекомендуется.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Требования к системе

Для работы с речевым пакетом SDK в Windows требуется <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">распространяемый компонент <span class="docon docon-navigate-external x-hidden-focus"></span> Microsoft Visual C++ для Visual Studio 2019</a> в системе.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Установка для x86 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Установка для x64 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Установка для ARMx64 <span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

Для ввода с микрофона необходимо установить библиотеки Media Foundation. Эти библиотеки входят в Windows 10 и Windows Server 2016. Пакет SDK для службы "Речь" можно использовать без этих библиотек, если в качестве аудиоустройства ввода не используется микрофон.

Необходимые файлы пакета SDK для службы "Речь" можно развернуть в том же каталоге, что и приложение. Таким образом, приложение сможет получать прямой доступ к библиотекам. Убедитесь, что выбрана правильная версия (x86/x64), соответствующая Вашему приложению.

| Имя                                            | Функция                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | Основной пакет SDK, необходимый для развертывания управляемого и машинного кода |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Требуется для развертывания управляемого кода                      |

> [!NOTE]
> Начиная с выпуска 1.3.0 файл `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (отгружен в предыдущих выпусках) больше не требуется. Теперь функциональность интегрирована в пакет SDK для Core.

> [!IMPORTANT]
> Для проекта Windows Forms приложение (.NET Framework) C# Убедитесь, что библиотеки включены в параметры развертывания проекта. Это можно проверить в разделе `Properties -> Publish Section` . Нажмите кнопку `Application Files` и найдите соответствующие библиотеки в списке Прокрутка вниз. Убедитесь, что для параметра задано значение `Included` . Visual Studio будет включать файл при публикации или развертывании проекта.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
