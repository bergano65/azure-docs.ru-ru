---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 2ab636679e59536a2ddfaa8603dc2da45811cd2f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99213719"
---
:::row:::
    :::column span="3":::
        Пакет SDK для Java для Android упакован как <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR (библиотека Android) <span class="docon docon-navigate-external x-hidden-focus"></span> </a>, который включает необходимые библиотеки и необходимые разрешения Android. Она размещена в репозитории Maven в `https://csspeechstorage.blob.core.windows.net/maven/` в виде пакета `com.microsoft.cognitiveservices.speech:client-sdk:1.15.0`.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Чтобы использовать этот пакет из проекта Android Studio, внесите следующие изменения:

1. В файле *Build. gradle* на уровне проекта добавьте в раздел следующую команду `repositories` :
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. В файле *Build. gradle* на уровне модуля добавьте в раздел следующую команду `dependencies` :
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.15.0'
  ```

Пакет SDK для Java также входит в [пакет SDK для устройств распознавания речи](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Дополнительные ресурсы

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Исходный код краткого руководства по Java для Android <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Исходный код краткого руководства по среде выполнения Java (JRE) <span class="docon docon-navigate-external x-hidden-focus"></span></a>
