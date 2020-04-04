---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 59835890a5baaf050868951ea683fd97b795ce3e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656543"
---
:::row:::
    :::column span="3":::
        Java SDK для Android упакован как <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR (Android <span class="docon docon-navigate-external x-hidden-focus"> </span>Library), </a>который включает в себя необходимые библиотеки и необходимые разрешения Android. Она размещена в репозитории Maven в `https://csspeechstorage.blob.core.windows.net/maven/` в виде пакета `com.microsoft.cognitiveservices.speech:client-sdk:1.11.0`.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Чтобы использовать этот пакет из проекта Android Studio, внесите следующие изменения:

1. В файле *build.gradle* уровня проекта добавьте `repository` следующее в раздел:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. В файле *build.gradle* уровня модуля `dependencies` добавьте в раздел следующее:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
  ```

Пакет SDK для Java также входит в [пакет SDK для устройств распознавания речи](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Дополнительные ресурсы

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android конкретных Java быстрый запуск исходный код<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Java Runtime (JRE) быстрый запуск исходного кода<span class="docon docon-navigate-external x-hidden-focus"></span></a>