---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5a06a0663601c221dd456b9cf4437cb9f32a18f8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399939"
---
:::row:::
    :::column span="3":::
        СДК «СДК» доступен на Windows, Linux и macOS. Для получения дополнительной информации <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">см. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>Пакет СЗ NuGet

SDK речи СДК может быть установлен от `Install-Package` **менеджера пакетов** со следующей командой.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>Файлы бинарных файлов и заголовков СЗ

Кроме того, sDK речи СЗ может быть установлен из бинарных файлов. Скачать SDK в виде <a href="https://aka.ms/csspeech/linuxbinary" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> пакета .tar</a> и распаковать файлы в каталоге по вашему выбору. Содержимое этого пакета (включающая файлы заголовка для целевых архитектур x86 и x64) структурировано следующим образом:

  | путь                   | Описание                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Лицензия                                              |
  | `ThirdPartyNotices.md` | Уведомления сторонних производителей                                  |
  | `include`              | Заголовок файлов для СЗЗ                                 |
  | `lib/x64`              | Собственная библиотека x64 для связывания с приложением |
  | `lib/x86`              | Собственная библиотека x86 для связывания с приложением |

  Чтобы создать приложение, скопируйте или переместите необходимые двоичные файлы (и библиотеки) в среду разработки. Включите их как обязательные в процесс сборки.

#### <a name="additional-resources"></a>Дополнительные ресурсы

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Исходный код Windows, Linux и macOS<span class="docon docon-navigate-external x-hidden-focus"></span></a>