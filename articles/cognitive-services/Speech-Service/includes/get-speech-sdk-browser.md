---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-javascript
ms.openlocfilehash: 043a37c604f56c70fb568fd027913380b80e3452
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87425104"
---
:::row:::
    :::column span="3":::
        Пакет SDK для распознавания речи для JavaScript доступен в виде пакета NPM. см. раздел <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Microsoft-cognitiveservices- <span class="docon docon-navigate-external x-hidden-focus"></span> Speech-SDK</a> и сопутствующий репозиторий GitHub <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">-Services-Speech-SDK <span class="docon docon-navigate-external x-hidden-focus"></span> -JS </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Несмотря на то, что пакет SDK для распознавания речи для JavaScript доступен в виде пакета NPM, так как клиентские веб-браузеры и Node.js могут использовать его, рассмотрите различные архитектурные аспекты каждой среды. Например, <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">объектная модель документов (DOM) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> недоступна для приложений на стороне сервера, так как <a href="https://nodejs.org/api/fs.html" target="_blank">Файловая система <span class="docon docon-navigate-external x-hidden-focus"></span> </a> недоступна для клиентских приложений.

### <a name="nodejs-package-manager-npm"></a>Диспетчер пакетов Node.js (NPM)

Чтобы установить пакет SDK для распознавания речи для JavaScript, выполните следующую `npm install` команду ниже.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>Тег HTML-скрипта

Кроме того, можно напрямую включить `<script>` тег в `<head>` элемент HTML, используя <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **жсделивр** NPM синдикации <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@latest/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

Дополнительные сведения см. в <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">кратком руководстве <span class="docon docon-navigate-external x-hidden-focus"></span> по РЕЧЕВому пакету SDK для веб-браузера </a>.
