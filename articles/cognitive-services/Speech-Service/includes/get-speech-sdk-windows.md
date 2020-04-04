---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: dea6a1afaa2348fc5054bee20c534936dcafe5b5
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656582"
---
:::row:::
    :::column span="3":::
        Speech SDK поддерживает Windows 10 и Windows Server 2016, или более поздние версии. Предыдущие версии официально **не** поддерживаются. Можно использовать части речи SDK с более ранними версиями Windows, хотя это не рекомендуется.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Требования к системе

Речь SDK на Windows требует <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Microsoft Visual C е Redistributable <span class="docon docon-navigate-external x-hidden-focus"></span> для Visual Studio 2019</a> на системе.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Установка для x86<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Установка для x64<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Установка для ARMx64<span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

Для ввода с микрофона необходимо установить библиотеки Media Foundation. Эти библиотеки входят в Windows 10 и Windows Server 2016. Пакет SDK для службы "Речь" можно использовать без этих библиотек, если в качестве аудиоустройства ввода не используется микрофон.

Необходимые файлы пакета SDK для службы "Речь" можно развернуть в том же каталоге, что и приложение. Таким образом, приложение сможет получать прямой доступ к библиотекам. Убедитесь, что вы выберите правильную версию (x86/x64), которая соответствует вашему приложению.

| name                                            | Функция                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | Основной пакет SDK, необходимый для развертывания управляемого и машинного кода |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Требуется для развертывания управляемого кода                      |

> [!NOTE]
> Начиная с выпуска 1.3.0 `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` файл (отправлен в предыдущих релизах) больше не нужен. Функциональность теперь интегрирована в ядро SDK.

> [!IMPORTANT]
> В проекте Windows Forms App (.NET Framework) На C, убедитесь, что библиотеки включены в настройки развертывания проекта. Вы можете проверить `Properties -> Publish Section`это под . Нажмите `Application Files` кнопку и найдите соответствующие библиотеки из списка прокрутки вниз. Убедитесь, что значение `Included`настроено на. Visual Studio будет включать файл при публикации/развертывании проекта.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
