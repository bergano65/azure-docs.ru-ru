---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5a06a0663601c221dd456b9cf4437cb9f32a18f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399939"
---
:::row:::
    :::column span="3":::
        ПАКЕТ C++ для распознавания речи доступен в Windows, Linux и macOS. Дополнительные сведения см. в разделе <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft. CognitiveServices. <span class="docon docon-navigate-external x-hidden-focus"> </span>Speech </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>Пакет NuGet C++

ПАКЕТ C++ для распознавания речи можно установить из **диспетчера пакетов** с помощью следующей `Install-Package` команды.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>Двоичные данные и файлы заголовков C++

Кроме того, пакет SDK для C++ для распознавания речи можно установить из двоичных файлов. Скачайте пакет SDK в виде <a href="https://aka.ms/csspeech/linuxbinary" target="_blank">tar-пакета <span class="docon docon-navigate-external x-hidden-focus"></span> </a> и распакуйте файлы в каталоге по своему усмотрению. Содержимое этого пакета (включающее файлы заголовков для архитектур x86 и x64) структурировано следующим образом:

  | Path                   | Описание                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Лицензия                                              |
  | `ThirdPartyNotices.md` | Уведомления сторонних производителей                                  |
  | `include`              | Файлы заголовков для C++                                 |
  | `lib/x64`              | Собственная библиотека x64 для связывания с приложением |
  | `lib/x86`              | Собственная библиотека x86 для связывания с приложением |

  Чтобы создать приложение, скопируйте или переместите необходимые двоичные файлы (и библиотеки) в среду разработки. Включите их как обязательные в процесс сборки.

#### <a name="additional-resources"></a>Дополнительные ресурсы

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Исходный код C++ краткое руководство по Windows, Linux и macOS<span class="docon docon-navigate-external x-hidden-focus"></span></a>