---
title: Теги Cognitive Servicesного образа контейнера
titleSuffix: Azure Cognitive Services
description: Полный список тегов образа контейнера службы.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/18/2019
ms.author: dapine
ms.openlocfilehash: 0d8c7a36582c30975f3a408a2ea6e95d39e560ef
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173753"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Теги образа контейнера Cognitive Services Azure

Azure Cognitive Services предлагает множество образов контейнеров. Реестры контейнеров и соответствующие репозитории зависят от образов контейнеров. Каждое имя образа контейнера предлагает несколько тегов. Тег образа контейнера — это механизм управления версиями образа контейнера. Эта статья предназначена для использования в качестве исчерпывающего справочника по перечислению всех Cognitive Services образов контейнеров и их доступных тегов.

> [!TIP]
> При использовании [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)Обратите внимание на регистр реестра контейнеров, репозитория, имени образа контейнера и соответствующего тега, так как они **чувствительны к регистру**.

## <a name="anomaly-detector"></a>Детектор аномалий

Образ контейнера [детектора аномалий][ad-containers] можно найти в реестре контейнеров `containerpreview.azurecr.io`. Он находится в репозитории `microsoft` и называется `cognitive-services-anomaly-detector`. Полное имя образа контейнера —, `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`.

Этот образ контейнера имеет следующие доступные Теги:

| Теги изображений | Примечания |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008500001-amd64-preview` | |

## <a name="computer-vision"></a>API компьютерного зрения

Образ контейнера [компьютерное зрение][cv-containers] можно найти в `containerpreview.azurecr.io` реестре контейнеров. Он находится в репозитории `microsoft` и называется `cognitive-services-read`. Полное имя образа контейнера —, `containerpreview.azurecr.io/microsoft/cognitive-services-read`.

Этот образ контейнера имеет следующие доступные Теги:

| Теги изображений | Примечания |
|------------|:------|
| `latest` | |
| `1.1.009920003-amd64-preview` | |
| `1.1.009910003-amd64-preview` | |

## <a name="face"></a>API распознавания лиц

Образ контейнера [лиц][fa-containers] можно найти в реестре контейнеров `containerpreview.azurecr.io`. Он находится в репозитории `microsoft` и называется `cognitive-services-face`. Полное имя образа контейнера —, `containerpreview.azurecr.io/microsoft/cognitive-services-face`.

Этот образ контейнера имеет следующие доступные Теги:

| Теги изображений | Примечания |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008710001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |
| `1.1.006490002-amd64-preview` | |
| `1.0.005940002-amd64-preview` | |
| `1.0.005550001-amd64-preview` | |

## <a name="form-recognizer"></a>Распознаватель документов

Образ контейнера [распознавателя форм][fr-containers] можно найти в `containerpreview.azurecr.io` реестре контейнеров. Он находится в репозитории `microsoft` и называется `cognitive-services-form-recognizer`. Полное имя образа контейнера —, `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`.

Этот образ контейнера имеет следующие доступные Теги:

| Теги изображений | Примечания |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008640001-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |

## <a name="language-understanding-luis"></a>Распознавание речи (LUIS)

Образ контейнера [Luis][lu-containers] можно найти в `mcr.microsoft.com` синдикации реестра контейнеров. Он находится в репозитории `azure-cognitive-services` и называется `luis`. Полное имя образа контейнера —, `mcr.microsoft.com/azure-cognitive-services/luis`.

Этот образ контейнера имеет следующие доступные Теги:

| Теги изображений | Примечания |
|------------|:------|
| `latest` | |
| `1.1.010330004-amd64-preview` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008710001-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.008010002-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.007020001-amd64-preview` | |

## <a name="custom-speech-to-text"></a>Пользовательское распознавание речи к тексту

Образ контейнера [пользовательское распознавание речи в текст][sp-cstt] можно найти в `containerpreview.azurecr.io` реестре контейнеров. Он находится в репозитории `microsoft` и называется `cognitive-services-custom-speech-to-text`. Полное имя образа контейнера —, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`.

Этот образ контейнера имеет следующие доступные Теги:

| Теги изображений | Примечания |
|------------|:------|
| `latest` | |
| `2.0.0-amd64-preview` | |

## <a name="custom-text-to-speech"></a>Пользовательский текст в речь

Пользовательский образ контейнера преобразования [текста в речь][sp-ctts] можно найти в реестре контейнеров `containerpreview.azurecr.io`. Он находится в репозитории `microsoft` и называется `cognitive-services-custom-text-to-speech`. Полное имя образа контейнера —, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`.

Этот образ контейнера имеет следующие доступные Теги:

| Теги изображений | Примечания |
|------------|:------|
| `latest` | |
| `1.3.0-amd64-preview` | |

## <a name="speech-to-text"></a>Преобразование речи в текст.

Образ контейнера [для преобразования речи в текст][sp-stt] можно найти в реестре контейнеров `containerpreview.azurecr.io`. Он находится в репозитории `microsoft` и называется `cognitive-services-speech-to-text`. Полное имя образа контейнера —, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`.

Этот образ контейнера имеет следующие доступные Теги:

| Теги изображений | Примечания |
|------------|:------|
| `latest` | Образ контейнера с языковым стандартом `en-US`. |
| `2.0.0-amd64-ar-eg-preview` | Образ контейнера с языковым стандартом `ar-EG`. |
| `2.0.0-amd64-ca-es-preview` | Образ контейнера с языковым стандартом `ca-ES`. |
| `2.0.0-amd64-da-dk-preview` | Образ контейнера с языковым стандартом `da-DK`. |
| `2.0.0-amd64-de-de-preview` | Образ контейнера с языковым стандартом `de-DE`. |
| `2.0.0-amd64-en-au-preview` | Образ контейнера с языковым стандартом `en-AU`. |
| `2.0.0-amd64-en-ca-preview` | Образ контейнера с языковым стандартом `en-CA`. |
| `2.0.0-amd64-en-gb-preview` | Образ контейнера с языковым стандартом `en-GB`. |
| `2.0.0-amd64-en-in-preview` | Образ контейнера с языковым стандартом `en-IN`. |
| `2.0.0-amd64-en-nz-preview` | Образ контейнера с языковым стандартом `en-NZ`. |
| `2.0.0-amd64-en-us-preview` | Образ контейнера с языковым стандартом `en-US`. |
| `2.0.0-amd64-es-es-preview` | Образ контейнера с языковым стандартом `es-ES`. |
| `2.0.0-amd64-es-mx-preview` | Образ контейнера с языковым стандартом `es-MX`. |
| `2.0.0-amd64-fi-fi-preview` | Образ контейнера с языковым стандартом `fi-FI`. |
| `2.0.0-amd64-fr-ca-preview` | Образ контейнера с языковым стандартом `fr-CA`. |
| `2.0.0-amd64-fr-fr-preview` | Образ контейнера с языковым стандартом `fr-FR`. |
| `2.0.0-amd64-hi-in-preview` | Образ контейнера с языковым стандартом `hi-IN`. |
| `2.0.0-amd64-it-it-preview` | Образ контейнера с языковым стандартом `it-IT`. |
| `2.0.0-amd64-ja-jp-preview` | Образ контейнера с языковым стандартом `ja-JP`. |
| `2.0.0-amd64-ko-kr-preview` | Образ контейнера с языковым стандартом `ko-KR`. |
| `2.0.0-amd64-nb-no-preview` | Образ контейнера с языковым стандартом `nb-NO`. |
| `2.0.0-amd64-nl-nl-preview` | Образ контейнера с языковым стандартом `nl-NL`. |
| `2.0.0-amd64-pl-pl-preview` | Образ контейнера с языковым стандартом `pl-PL`. |
| `2.0.0-amd64-pt-br-preview` | Образ контейнера с языковым стандартом `pt-BR`. |
| `2.0.0-amd64-pt-pt-preview` | Образ контейнера с языковым стандартом `pt-PT`. |
| `2.0.0-amd64-ru-ru-preview` | Образ контейнера с языковым стандартом `ru-RU`. |
| `2.0.0-amd64-sv-se-preview` | Образ контейнера с языковым стандартом `sv-SE`. |
| `2.0.0-amd64-th-th-preview` | Образ контейнера с языковым стандартом `th-TH`. |
| `2.0.0-amd64-tr-tr-preview` | Образ контейнера с языковым стандартом `tr-TR`. |
| `2.0.0-amd64-zh-cn-preview` | Образ контейнера с языковым стандартом `zh-CN`. |
| `2.0.0-amd64-zh-hk-preview` | Образ контейнера с языковым стандартом `zh-HK`. |
| `2.0.0-amd64-zh-tw-preview` | Образ контейнера с языковым стандартом `zh-TW`. |
| `1.2.0-amd64-de-de-preview` | Образ контейнера с языковым стандартом `de-DE`. |
| `1.2.0-amd64-en-au-preview` | Образ контейнера с языковым стандартом `en-AU`. |
| `1.2.0-amd64-en-ca-preview` | Образ контейнера с языковым стандартом `en-CA`. |
| `1.2.0-amd64-en-gb-preview` | Образ контейнера с языковым стандартом `en-GB`. |
| `1.2.0-amd64-en-in-preview` | Образ контейнера с языковым стандартом `en-IN`. |
| `1.2.0-amd64-en-us-preview` | Образ контейнера с языковым стандартом `en-US`. |
| `1.2.0-amd64-es-es-preview` | Образ контейнера с языковым стандартом `es-ES`. |
| `1.2.0-amd64-es-mx-preview` | Образ контейнера с языковым стандартом `es-MX`. |
| `1.2.0-amd64-fr-ca-preview` | Образ контейнера с языковым стандартом `fr-CA`. |
| `1.2.0-amd64-fr-fr-preview` | Образ контейнера с языковым стандартом `fr-FR`. |
| `1.2.0-amd64-it-it-preview` | Образ контейнера с языковым стандартом `it-IT`. |
| `1.2.0-amd64-ja-jp-preview` | Образ контейнера с языковым стандартом `ja-JP`. |
| `1.2.0-amd64-pt-br-preview` | Образ контейнера с языковым стандартом `pt-BR`. |
| `1.2.0-amd64-zh-cn-preview` | Образ контейнера с языковым стандартом `zh-CN`. |
| `1.1.3-amd64-de-de-preview` | Образ контейнера с языковым стандартом `de-DE`. |
| `1.1.3-amd64-en-au-preview` | Образ контейнера с языковым стандартом `en-AU`. |
| `1.1.3-amd64-en-ca-preview` | Образ контейнера с языковым стандартом `en-CA`. |
| `1.1.3-amd64-en-gb-preview` | Образ контейнера с языковым стандартом `en-GB`. |
| `1.1.3-amd64-en-in-preview` | Образ контейнера с языковым стандартом `en-IN`. |
| `1.1.3-amd64-en-us-preview` | Образ контейнера с языковым стандартом `en-US`. |
| `1.1.3-amd64-es-es-preview` | Образ контейнера с языковым стандартом `es-ES`. |
| `1.1.3-amd64-es-mx-preview` | Образ контейнера с языковым стандартом `es-MX`. |
| `1.1.3-amd64-fr-ca-preview` | Образ контейнера с языковым стандартом `fr-CA`. |
| `1.1.3-amd64-fr-fr-preview` | Образ контейнера с языковым стандартом `fr-FR`. |
| `1.1.3-amd64-it-it-preview` | Образ контейнера с языковым стандартом `it-IT`. |
| `1.1.3-amd64-ja-jp-preview` | Образ контейнера с языковым стандартом `ja-JP`. |
| `1.1.3-amd64-pt-br-preview` | Образ контейнера с языковым стандартом `pt-BR`. |
| `1.1.3-amd64-zh-cn-preview` | Образ контейнера с языковым стандартом `zh-CN`. |
| `1.1.2-amd64-de-de-preview` | Образ контейнера с языковым стандартом `de-DE`. |
| `1.1.2-amd64-en-au-preview` | Образ контейнера с языковым стандартом `en-AU`. |
| `1.1.2-amd64-en-ca-preview` | Образ контейнера с языковым стандартом `en-CA`. |
| `1.1.2-amd64-en-gb-preview` | Образ контейнера с языковым стандартом `en-GB`. |
| `1.1.2-amd64-en-in-preview` | Образ контейнера с языковым стандартом `en-IN`. |
| `1.1.2-amd64-en-us-preview` | Образ контейнера с языковым стандартом `en-US`. |
| `1.1.2-amd64-es-es-preview` | Образ контейнера с языковым стандартом `es-ES`. |
| `1.1.2-amd64-es-mx-preview` | Образ контейнера с языковым стандартом `es-MX`. |
| `1.1.2-amd64-fr-ca-preview` | Образ контейнера с языковым стандартом `fr-CA`. |
| `1.1.2-amd64-fr-fr-preview` | Образ контейнера с языковым стандартом `fr-FR`. |
| `1.1.2-amd64-it-it-preview` | Образ контейнера с языковым стандартом `it-IT`. |
| `1.1.2-amd64-ja-jp-preview` | Образ контейнера с языковым стандартом `ja-JP`. |
| `1.1.2-amd64-pt-br-preview` | Образ контейнера с языковым стандартом `pt-BR`. |
| `1.1.2-amd64-zh-cn-preview` | Образ контейнера с языковым стандартом `zh-CN`. |
| `1.1.1-amd64-de-de-preview` | Образ контейнера с языковым стандартом `de-DE`. |
| `1.1.1-amd64-en-au-preview` | Образ контейнера с языковым стандартом `en-AU`. |
| `1.1.1-amd64-en-ca-preview` | Образ контейнера с языковым стандартом `en-CA`. |
| `1.1.1-amd64-en-gb-preview` | Образ контейнера с языковым стандартом `en-GB`. |
| `1.1.1-amd64-en-in-preview` | Образ контейнера с языковым стандартом `en-IN`. |
| `1.1.1-amd64-en-us-preview` | Образ контейнера с языковым стандартом `en-US`. |
| `1.1.1-amd64-es-es-preview` | Образ контейнера с языковым стандартом `es-ES`. |
| `1.1.1-amd64-es-mx-preview` | Образ контейнера с языковым стандартом `es-MX`. |
| `1.1.1-amd64-fr-ca-preview` | Образ контейнера с языковым стандартом `fr-CA`. |
| `1.1.1-amd64-fr-fr-preview` | Образ контейнера с языковым стандартом `fr-FR`. |
| `1.1.1-amd64-it-it-preview` | Образ контейнера с языковым стандартом `it-IT`. |
| `1.1.1-amd64-ja-jp-preview` | Образ контейнера с языковым стандартом `ja-JP`. |
| `1.1.1-amd64-pt-br-preview` | Образ контейнера с языковым стандартом `pt-BR`. |
| `1.1.1-amd64-zh-cn-preview` | Образ контейнера с языковым стандартом `zh-CN`. |
| `1.1.0-amd64-de-de-preview` | Образ контейнера с языковым стандартом `de-DE`. |
| `1.1.0-amd64-en-au-preview` | Образ контейнера с языковым стандартом `en-AU`. |
| `1.1.0-amd64-en-ca-preview` | Образ контейнера с языковым стандартом `en-CA`. |
| `1.1.0-amd64-en-gb-preview` | Образ контейнера с языковым стандартом `en-GB`. |
| `1.1.0-amd64-en-in-preview` | Образ контейнера с языковым стандартом `en-IN`. |
| `1.1.0-amd64-en-us-preview` | Образ контейнера с языковым стандартом `en-US`. |
| `1.1.0-amd64-es-es-preview` | Образ контейнера с языковым стандартом `es-ES`. |
| `1.1.0-amd64-es-mx-preview` | Образ контейнера с языковым стандартом `es-MX`. |
| `1.1.0-amd64-fr-ca-preview` | Образ контейнера с языковым стандартом `fr-CA`. |
| `1.1.0-amd64-fr-fr-preview` | Образ контейнера с языковым стандартом `fr-FR`. |
| `1.1.0-amd64-it-it-preview` | Образ контейнера с языковым стандартом `it-IT`. |
| `1.1.0-amd64-ja-jp-preview` | Образ контейнера с языковым стандартом `ja-JP`. |
| `1.1.0-amd64-pt-br-preview` | Образ контейнера с языковым стандартом `pt-BR`. |
| `1.1.0-amd64-zh-cn-preview` | Образ контейнера с языковым стандартом `zh-CN`. |
| `1.0.0-amd64-de-de-preview` | Образ контейнера с языковым стандартом `de-DE`. |
| `1.0.0-amd64-en-au-preview` | Образ контейнера с языковым стандартом `en-AU`. |
| `1.0.0-amd64-en-ca-preview` | Образ контейнера с языковым стандартом `en-CA`. |
| `1.0.0-amd64-en-gb-preview` | Образ контейнера с языковым стандартом `en-GB`. |
| `1.0.0-amd64-en-in-preview` | Образ контейнера с языковым стандартом `en-IN`. |
| `1.0.0-amd64-en-us-preview` | Образ контейнера с языковым стандартом `en-US`. |
| `1.0.0-amd64-es-es-preview` | Образ контейнера с языковым стандартом `es-ES`. |
| `1.0.0-amd64-es-mx-preview` | Образ контейнера с языковым стандартом `es-MX`. |
| `1.0.0-amd64-fr-ca-preview` | Образ контейнера с языковым стандартом `fr-CA`. |
| `1.0.0-amd64-fr-fr-preview` | Образ контейнера с языковым стандартом `fr-FR`. |
| `1.0.0-amd64-it-it-preview` | Образ контейнера с языковым стандартом `it-IT`. |
| `1.0.0-amd64-ja-jp-preview` | Образ контейнера с языковым стандартом `ja-JP`. |
| `1.0.0-amd64-pt-br-preview` | Образ контейнера с языковым стандартом `pt-BR`. |
| `1.0.0-amd64-zh-cn-preview` | Образ контейнера с языковым стандартом `zh-CN`. |

## <a name="text-to-speech"></a>Преобразование текста в речь

Образ контейнера преобразования [текста в речь][sp-tts] можно найти в реестре контейнеров `containerpreview.azurecr.io`. Он находится в репозитории `microsoft` и называется `cognitive-services-text-to-speech`. Полное имя образа контейнера —, `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`.

Этот образ контейнера имеет следующие доступные Теги:

| Теги изображений | Примечания |
|------------|:------|
| `latest` | Образ контейнера с языковым стандартом `en-US` и `en-US-JessaRUS` голоса. |
| `1.3.0-amd64-ar-eg-hoda-preview` | Образ контейнера с языковым стандартом `ar-EG` и `ar-EG-Hoda` голоса. |
| `1.3.0-amd64-ar-sa-naayf-preview` | Образ контейнера с языковым стандартом `ar-SA` и `ar-SA-Naayf` голоса. |
| `1.3.0-amd64-bg-bg-ivan-preview` | Образ контейнера с языковым стандартом `bg-BG` и `bg-BG-Ivan` голоса. |
| `1.3.0-amd64-ca-es-herenarus-preview` | Образ контейнера с языковым стандартом `ca-ES` и `ca-ES-HerenaRUS` голоса. |
| `1.3.0-amd64-cs-cz-jakub-preview` | Образ контейнера с языковым стандартом `cs-CZ` и `cs-CZ-Jakub` голоса. |
| `1.3.0-amd64-da-dk-hellerus-preview` | Образ контейнера с языковым стандартом `da-DK` и `da-DK-HelleRUS` голоса. |
| `1.3.0-amd64-de-at-michael-preview` | Образ контейнера с языковым стандартом `de-AT` и `de-AT-Michael` голоса. |
| `1.3.0-amd64-de-ch-karsten-preview` | Образ контейнера с языковым стандартом `de-CH` и `de-CH-Karsten` голоса. |
| `1.3.0-amd64-de-de-hedda-preview` | Образ контейнера с языковым стандартом `de-DE` и `de-DE-Hedda` голоса. |
| `1.3.0-amd64-de-de-heddarus-preview` | Образ контейнера с языковым стандартом `de-DE` и `de-DE-Hedda` голоса. |
| `1.3.0-amd64-de-de-heddarus-preview` | Образ контейнера с языковым стандартом `de-DE` и `de-DE-HeddaRUS` голоса. |
| `1.3.0-amd64-de-de-stefan-apollo-preview` | Образ контейнера с языковым стандартом `de-DE` и `de-DE-Stefan-Apollo` голоса. |
| `1.3.0-amd64-el-gr-stefanos-preview` | Образ контейнера с языковым стандартом `el-GR` и `el-GR-Stefanos` голоса. |
| `1.3.0-amd64-en-au-catherine-preview` | Образ контейнера с языковым стандартом `en-AU` и `en-AU-Catherine` голоса. |
| `1.3.0-amd64-en-au-hayleyrus-preview` | Образ контейнера с языковым стандартом `en-AU` и `en-AU-HayleyRUS` голоса. |
| `1.3.0-amd64-en-ca-heatherrus-preview` | Образ контейнера с языковым стандартом `en-CA` и `en-CA-HeatherRUS` голоса. |
| `1.3.0-amd64-en-ca-linda-preview` | Образ контейнера с языковым стандартом `en-CA` и `en-CA-Linda` голоса. |
| `1.3.0-amd64-en-gb-george-apollo-preview` | Образ контейнера с языковым стандартом `en-GB` и `en-GB-George-Apollo` голоса. |
| `1.3.0-amd64-en-gb-hazelrus-preview` | Образ контейнера с языковым стандартом `en-GB` и `en-GB-HazelRUS` голоса. |
| `1.3.0-amd64-en-gb-susan-apollo-preview` | Образ контейнера с языковым стандартом `en-GB` и `en-GB-Susan-Apollo` голоса. |
| `1.3.0-amd64-en-ie-sean-preview` | Образ контейнера с языковым стандартом `en-IE` и `en-IE-Sean` голоса. |
| `1.3.0-amd64-en-in-heera-apollo-preview` | Образ контейнера с языковым стандартом `en-IN` и `en-IN-Heera-Apollo` голоса. |
| `1.3.0-amd64-en-in-priyarus-preview` | Образ контейнера с языковым стандартом `en-IN` и `en-IN-PriyaRUS` голоса. |
| `1.3.0-amd64-en-in-ravi-apollo-preview` | Образ контейнера с языковым стандартом `en-IN` и `en-IN-Ravi-Apollo` голоса. |
| `1.3.0-amd64-en-us-benjaminrus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-BenjaminRUS` голоса. |
| `1.3.0-amd64-en-us-guy24krus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-Guy24kRUS` голоса. |
| `1.3.0-amd64-en-us-jessa24krus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-Jessa24kRUS` голоса. |
| `1.3.0-amd64-en-us-jessarus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-JessaRUS` голоса. |
| `1.3.0-amd64-en-us-zirarus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-ZiraRUS` голоса. |
| `1.3.0-amd64-es-es-helenarus-preview` | Образ контейнера с языковым стандартом `es-ES` и `es-ES-HelenaRUS` голоса. |
| `1.3.0-amd64-es-es-laura-apollo-preview` | Образ контейнера с языковым стандартом `es-ES` и `es-ES-Laura-Apollo` голоса. |
| `1.3.0-amd64-es-es-pablo-apollo-preview` | Образ контейнера с языковым стандартом `es-ES` и `es-ES-Pablo-Apollo` голоса. |
| `1.3.0-amd64-es-mx-hildarus-preview` | Образ контейнера с языковым стандартом `es-MX` и `es-MX-HildaRUS` голоса. |
| `1.3.0-amd64-es-mx-raul-apollo-preview` | Образ контейнера с языковым стандартом `es-MX` и `es-MX-Raul-Apollo` голоса. |
| `1.3.0-amd64-fi-fi-heidirus-preview` | Образ контейнера с языковым стандартом `fi-FI` и `fi-FI-HeidiRUS` голоса. |
| `1.3.0-amd64-fr-ca-caroline-preview` | Образ контейнера с языковым стандартом `fr-CA` и `fr-CA-Caroline` голоса. |
| `1.3.0-amd64-fr-ca-harmonierus-preview` | Образ контейнера с языковым стандартом `fr-CA` и `fr-CA-HarmonieRUS` голоса. |
| `1.3.0-amd64-fr-ch-guillaume-preview` | Образ контейнера с языковым стандартом `fr-CH` и `fr-CH-Guillaume` голоса. |
| `1.3.0-amd64-fr-fr-hortenserus-preview` | Образ контейнера с языковым стандартом `fr-FR` и `fr-FR-HortenseRUS` голоса. |
| `1.3.0-amd64-fr-fr-julie-apollo-preview` | Образ контейнера с языковым стандартом `fr-FR` и `fr-FR-Julie-Apollo` голоса. |
| `1.3.0-amd64-fr-fr-paul-apollo-preview` | Образ контейнера с языковым стандартом `fr-FR` и `fr-FR-Paul-Apollo` голоса. |
| `1.3.0-amd64-he-il-asaf-preview` | Образ контейнера с языковым стандартом `he-IL` и `he-IL-Asaf` голоса. |
| `1.3.0-amd64-hi-in-hemant-preview` | Образ контейнера с языковым стандартом `hi-IN` и `hi-IN-Hemant` голоса. |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview` | Образ контейнера с языковым стандартом `hi-IN` и `hi-IN-Kalpana-Apollo` голоса. |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview` | Образ контейнера с языковым стандартом `hi-IN` и `hi-IN-Kalpana` голоса. |
| `1.3.0-amd64-hi-in-kalpana-preview` | Образ контейнера с языковым стандартом `hi-IN` и `hi-IN-Kalpana` голоса. |
| `1.3.0-amd64-hr-hr-matej-preview` | Образ контейнера с языковым стандартом `hr-HR` и `hr-HR-Matej` голоса. |
| `1.3.0-amd64-hu-hu-szabolcs-preview` | Образ контейнера с языковым стандартом `hu-HU` и `hu-HU-Szabolcs` голоса. |
| `1.3.0-amd64-id-id-andika-preview` | Образ контейнера с языковым стандартом `id-ID` и `id-ID-Andika` голоса. |
| `1.3.0-amd64-it-it-cosimo-apollo-preview` | Образ контейнера с языковым стандартом `it-IT` и `it-IT-Cosimo-Apollo` голоса. |
| `1.3.0-amd64-it-it-luciarus-preview` | Образ контейнера с языковым стандартом `it-IT` и `it-IT-LuciaRUS` голоса. |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview` | Образ контейнера с языковым стандартом `ja-JP` и `ja-JP-Ayumi-Apollo` голоса. |
| `1.3.0-amd64-ja-jp-harukarus-preview` | Образ контейнера с языковым стандартом `ja-JP` и `ja-JP-HarukaRUS` голоса. |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview` | Образ контейнера с языковым стандартом `ja-JP` и `ja-JP-Ichiro-Apollo` голоса. |
| `1.3.0-amd64-ko-kr-heamirus-preview` | Образ контейнера с языковым стандартом `ko-KR` и `ko-KR-HeamiRUS` голоса. |
| `1.3.0-amd64-ms-my-rizwan-preview` | Образ контейнера с языковым стандартом `ms-MY` и `ms-MY-Rizwan` голоса. |
| `1.3.0-amd64-nb-no-huldarus-preview` | Образ контейнера с языковым стандартом `nb-NO` и `nb-NO-HuldaRUS` голоса. |
| `1.3.0-amd64-nl-nl-hannarus-preview` | Образ контейнера с языковым стандартом `nl-NL` и `nl-NL-HannaRUS` голоса. |
| `1.3.0-amd64-pl-pl-paulinarus-preview` | Образ контейнера с языковым стандартом `pl-PL` и `pl-PL-PaulinaRUS` голоса. |
| `1.3.0-amd64-pt-br-daniel-apollo-preview` | Образ контейнера с языковым стандартом `pt-BR` и `pt-BR-Daniel-Apollo` голоса. |
| `1.3.0-amd64-pt-br-heloisarus-preview` | Образ контейнера с языковым стандартом `pt-BR` и `pt-BR-HeloisaRUS` голоса. |
| `1.3.0-amd64-pt-pt-heliarus-preview` | Образ контейнера с языковым стандартом `pt-PT` и `pt-PT-HeliaRUS` голоса. |
| `1.3.0-amd64-ro-ro-andrei-preview` | Образ контейнера с языковым стандартом `ro-RO` и `ro-RO-Andrei` голоса. |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview` | Образ контейнера с языковым стандартом `ru-RU` и `ru-RU-EkaterinaRUS` голоса. |
| `1.3.0-amd64-ru-ru-irina-apollo-preview` | Образ контейнера с языковым стандартом `ru-RU` и `ru-RU-Irina-Apollo` голоса. |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview` | Образ контейнера с языковым стандартом `ru-RU` и `ru-RU-Pavel-Apollo` голоса. |
| `1.3.0-amd64-sk-sk-filip-preview` | Образ контейнера с языковым стандартом `sk-SK` и `sk-SK-Filip` голоса. |
| `1.3.0-amd64-sl-si-lado-preview` | Образ контейнера с языковым стандартом `sl-SI` и `sl-SI-Lado` голоса. |
| `1.3.0-amd64-sv-se-hedvigrus-preview` | Образ контейнера с языковым стандартом `sv-SE` и `sv-SE-HedvigRUS` голоса. |
| `1.3.0-amd64-ta-in-valluvar-preview` | Образ контейнера с языковым стандартом `ta-IN` и `ta-IN-Valluvar` голоса. |
| `1.3.0-amd64-te-in-chitra-preview` | Образ контейнера с языковым стандартом `te-IN` и `te-IN-Chitra` голоса. |
| `1.3.0-amd64-th-th-pattara-preview` | Образ контейнера с языковым стандартом `th-TH` и `th-TH-Pattara` голоса. |
| `1.3.0-amd64-tr-tr-sedarus-preview` | Образ контейнера с языковым стандартом `tr-TR` и `tr-TR-SedaRUS` голоса. |
| `1.3.0-amd64-vi-vn-an-preview` | Образ контейнера с языковым стандартом `vi-VN` и `vi-VN-An` голоса. |
| `1.3.0-amd64-zh-cn-huihuirus-preview` | Образ контейнера с языковым стандартом `zh-CN` и `zh-CN-HuihuiRUS` голоса. |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Образ контейнера с языковым стандартом `zh-CN` и `zh-CN-Kangkang-Apollo` голоса. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview` | Образ контейнера с языковым стандартом `zh-CN` и `zh-CN-Yaoyao-Apollo` голоса. |
| `1.3.0-amd64-zh-hk-danny-apollo-preview` | Образ контейнера с языковым стандартом `zh-HK` и `zh-HK-Danny-Apollo` голоса. |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview` | Образ контейнера с языковым стандартом `zh-HK` и `zh-HK-Tracy-Apollo` голоса. |
| `1.3.0-amd64-zh-hk-tracyrus-preview` | Образ контейнера с языковым стандартом `zh-HK` и `zh-HK-TracyRUS` голоса. |
| `1.3.0-amd64-zh-tw-hanhanrus-preview` | Образ контейнера с языковым стандартом `zh-TW` и `zh-TW-HanHanRUS` голоса. |
| `1.3.0-amd64-zh-tw-yating-apollo-preview` | Образ контейнера с языковым стандартом `zh-TW` и `zh-TW-Yating-Apollo` голоса. |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview` | Образ контейнера с языковым стандартом `zh-TW` и `zh-TW-Zhiwei-Apollo` голоса. |
| `1.2.0-amd64-de-de-heddarus-preview` | Образ контейнера с языковым стандартом `de-DE` и `de-DE-Hedda` голоса. |
| `1.2.0-amd64-de-de-heddarus-preview` | Образ контейнера с языковым стандартом `de-DE` и `de-DE-HeddaRUS` голоса. |
| `1.2.0-amd64-de-de-stefan-apollo-preview` | Образ контейнера с языковым стандартом `de-DE` и `de-DE-Stefan-Apollo` голоса. |
| `1.2.0-amd64-en-au-catherine-preview` | Образ контейнера с языковым стандартом `en-AU` и `en-AU-Catherine` голоса. |
| `1.2.0-amd64-en-au-hayleyrus-preview` | Образ контейнера с языковым стандартом `en-AU` и `en-AU-HayleyRUS` голоса. |
| `1.2.0-amd64-en-gb-george-apollo-preview` | Образ контейнера с языковым стандартом `en-GB` и `en-GB-George-Apollo` голоса. |
| `1.2.0-amd64-en-gb-hazelrus-preview` | Образ контейнера с языковым стандартом `en-GB` и `en-GB-HazelRUS` голоса. |
| `1.2.0-amd64-en-gb-susan-apollo-preview` | Образ контейнера с языковым стандартом `en-GB` и `en-GB-Susan-Apollo` голоса. |
| `1.2.0-amd64-en-in-heera-apollo-preview` | Образ контейнера с языковым стандартом `en-IN` и `en-IN-Heera-Apollo` голоса. |
| `1.2.0-amd64-en-in-priyarus-preview` | Образ контейнера с языковым стандартом `en-IN` и `en-IN-PriyaRUS` голоса. |
| `1.2.0-amd64-en-in-ravi-apollo-preview` | Образ контейнера с языковым стандартом `en-IN` и `en-IN-Ravi-Apollo` голоса. |
| `1.2.0-amd64-en-us-benjaminrus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-BenjaminRUS` голоса. |
| `1.2.0-amd64-en-us-guy24krus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-Guy24kRUS` голоса. |
| `1.2.0-amd64-en-us-jessa24krus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-Jessa24kRUS` голоса. |
| `1.2.0-amd64-en-us-jessarus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-JessaRUS` голоса. |
| `1.2.0-amd64-en-us-zirarus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-ZiraRUS` голоса. |
| `1.2.0-amd64-es-es-helenarus-preview` | Образ контейнера с языковым стандартом `es-ES` и `es-ES-HelenaRUS` голоса. |
| `1.2.0-amd64-es-es-laura-apollo-preview` | Образ контейнера с языковым стандартом `es-ES` и `es-ES-Laura-Apollo` голоса. |
| `1.2.0-amd64-es-es-pablo-apollo-preview` | Образ контейнера с языковым стандартом `es-ES` и `es-ES-Pablo-Apollo` голоса. |
| `1.2.0-amd64-es-mx-hildarus-preview` | Образ контейнера с языковым стандартом `es-MX` и `es-MX-HildaRUS` голоса. |
| `1.2.0-amd64-es-mx-raul-apollo-preview` | Образ контейнера с языковым стандартом `es-MX` и `es-MX-Raul-Apollo` голоса. |
| `1.2.0-amd64-fr-ca-caroline-preview` | Образ контейнера с языковым стандартом `fr-CA` и `fr-CA-Caroline` голоса. |
| `1.2.0-amd64-fr-ca-harmonierus-preview` | Образ контейнера с языковым стандартом `fr-CA` и `fr-CA-HarmonieRUS` голоса. |
| `1.2.0-amd64-fr-fr-hortenserus-preview` | Образ контейнера с языковым стандартом `fr-FR` и `fr-FR-HortenseRUS` голоса. |
| `1.2.0-amd64-fr-fr-julie-apollo-preview` | Образ контейнера с языковым стандартом `fr-FR` и `fr-FR-Julie-Apollo` голоса. |
| `1.2.0-amd64-fr-fr-paul-apollo-preview` | Образ контейнера с языковым стандартом `fr-FR` и `fr-FR-Paul-Apollo` голоса. |
| `1.2.0-amd64-it-it-cosimo-apollo-preview` | Образ контейнера с языковым стандартом `it-IT` и `it-IT-Cosimo-Apollo` голоса. |
| `1.2.0-amd64-it-it-luciarus-preview` | Образ контейнера с языковым стандартом `it-IT` и `it-IT-LuciaRUS` голоса. |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview` | Образ контейнера с языковым стандартом `ja-JP` и `ja-JP-Ayumi-Apollo` голоса. |
| `1.2.0-amd64-ja-jp-harukarus-preview` | Образ контейнера с языковым стандартом `ja-JP` и `ja-JP-HarukaRUS` голоса. |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview` | Образ контейнера с языковым стандартом `ja-JP` и `ja-JP-Ichiro-Apollo` голоса. |
| `1.2.0-amd64-ko-kr-heamirus-preview` | Образ контейнера с языковым стандартом `ko-KR` и `ko-KR-HeamiRUS` голоса. |
| `1.2.0-amd64-pt-br-daniel-apollo-preview` | Образ контейнера с языковым стандартом `pt-BR` и `pt-BR-Daniel-Apollo` голоса. |
| `1.2.0-amd64-pt-br-heloisarus-preview` | Образ контейнера с языковым стандартом `pt-BR` и `pt-BR-HeloisaRUS` голоса. |
| `1.2.0-amd64-zh-cn-huihuirus-preview` | Образ контейнера с языковым стандартом `zh-CN` и `zh-CN-HuihuiRUS` голоса. |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Образ контейнера с языковым стандартом `zh-CN` и `zh-CN-Kangkang-Apollo` голоса. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview` | Образ контейнера с языковым стандартом `zh-CN` и `zh-CN-Yaoyao-Apollo` голоса. |
| `1.1.0-amd64-de-de-hedda-preview` | Образ контейнера с языковым стандартом `de-DE` и `de-DE-Hedda` голоса. |
| `1.1.0-amd64-de-de-heddarus-preview` | Образ контейнера с языковым стандартом `de-DE` и `de-DE-Hedda` голоса. |
| `1.1.0-amd64-de-de-heddarus-preview` | Образ контейнера с языковым стандартом `de-DE` и `de-DE-HeddaRUS` голоса. |
| `1.1.0-amd64-de-de-stefan-apollo-preview` | Образ контейнера с языковым стандартом `de-DE` и `de-DE-Stefan-Apollo` голоса. |
| `1.1.0-amd64-en-au-catherine-preview` | Образ контейнера с языковым стандартом `en-AU` и `en-AU-Catherine` голоса. |
| `1.1.0-amd64-en-au-hayleyrus-preview` | Образ контейнера с языковым стандартом `en-AU` и `en-AU-HayleyRUS` голоса. |
| `1.1.0-amd64-en-gb-george-apollo-preview` | Образ контейнера с языковым стандартом `en-GB` и `en-GB-George-Apollo` голоса. |
| `1.1.0-amd64-en-gb-hazelrus-preview` | Образ контейнера с языковым стандартом `en-GB` и `en-GB-HazelRUS` голоса. |
| `1.1.0-amd64-en-gb-susan-apollo-preview` | Образ контейнера с языковым стандартом `en-GB` и `en-GB-Susan-Apollo` голоса. |
| `1.1.0-amd64-en-in-heera-apollo-preview` | Образ контейнера с языковым стандартом `en-IN` и `en-IN-Heera-Apollo` голоса. |
| `1.1.0-amd64-en-in-priyarus-preview` | Образ контейнера с языковым стандартом `en-IN` и `en-IN-PriyaRUS` голоса. |
| `1.1.0-amd64-en-in-ravi-apollo-preview` | Образ контейнера с языковым стандартом `en-IN` и `en-IN-Ravi-Apollo` голоса. |
| `1.1.0-amd64-en-us-benjaminrus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-BenjaminRUS` голоса. |
| `1.1.0-amd64-en-us-guy24krus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-Guy24kRUS` голоса. |
| `1.1.0-amd64-en-us-jessa24krus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-Jessa24kRUS` голоса. |
| `1.1.0-amd64-en-us-jessarus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-JessaRUS` голоса. |
| `1.1.0-amd64-en-us-zirarus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-ZiraRUS` голоса. |
| `1.1.0-amd64-es-es-helenarus-preview` | Образ контейнера с языковым стандартом `es-ES` и `es-ES-HelenaRUS` голоса. |
| `1.1.0-amd64-es-es-laura-apollo-preview` | Образ контейнера с языковым стандартом `es-ES` и `es-ES-Laura-Apollo` голоса. |
| `1.1.0-amd64-es-es-pablo-apollo-preview` | Образ контейнера с языковым стандартом `es-ES` и `es-ES-Pablo-Apollo` голоса. |
| `1.1.0-amd64-es-mx-hildarus-preview` | Образ контейнера с языковым стандартом `es-MX` и `es-MX-HildaRUS` голоса. |
| `1.1.0-amd64-es-mx-raul-apollo-preview` | Образ контейнера с языковым стандартом `es-MX` и `es-MX-Raul-Apollo` голоса. |
| `1.1.0-amd64-fr-ca-caroline-preview` | Образ контейнера с языковым стандартом `fr-CA` и `fr-CA-Caroline` голоса. |
| `1.1.0-amd64-fr-ca-harmonierus-preview` | Образ контейнера с языковым стандартом `fr-CA` и `fr-CA-HarmonieRUS` голоса. |
| `1.1.0-amd64-fr-fr-hortenserus-preview` | Образ контейнера с языковым стандартом `fr-FR` и `fr-FR-HortenseRUS` голоса. |
| `1.1.0-amd64-fr-fr-julie-apollo-preview` | Образ контейнера с языковым стандартом `fr-FR` и `fr-FR-Julie-Apollo` голоса. |
| `1.1.0-amd64-fr-fr-paul-apollo-preview` | Образ контейнера с языковым стандартом `fr-FR` и `fr-FR-Paul-Apollo` голоса. |
| `1.1.0-amd64-it-it-cosimo-apollo-preview` | Образ контейнера с языковым стандартом `it-IT` и `it-IT-Cosimo-Apollo` голоса. |
| `1.1.0-amd64-it-it-luciarus-preview` | Образ контейнера с языковым стандартом `it-IT` и `it-IT-LuciaRUS` голоса. |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview` | Образ контейнера с языковым стандартом `ja-JP` и `ja-JP-Ayumi-Apollo` голоса. |
| `1.1.0-amd64-ja-jp-harukarus-preview` | Образ контейнера с языковым стандартом `ja-JP` и `ja-JP-HarukaRUS` голоса. |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview` | Образ контейнера с языковым стандартом `ja-JP` и `ja-JP-Ichiro-Apollo` голоса. |
| `1.1.0-amd64-ko-kr-heamirus-preview` | Образ контейнера с языковым стандартом `ko-KR` и `ko-KR-HeamiRUS` голоса. |
| `1.1.0-amd64-pt-br-daniel-apollo-preview` | Образ контейнера с языковым стандартом `pt-BR` и `pt-BR-Daniel-Apollo` голоса. |
| `1.1.0-amd64-pt-br-heloisarus-preview` | Образ контейнера с языковым стандартом `pt-BR` и `pt-BR-HeloisaRUS` голоса. |
| `1.1.0-amd64-zh-cn-huihuirus-preview` | Образ контейнера с языковым стандартом `zh-CN` и `zh-CN-HuihuiRUS` голоса. |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Образ контейнера с языковым стандартом `zh-CN` и `zh-CN-Kangkang-Apollo` голоса. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview` | Образ контейнера с языковым стандартом `zh-CN` и `zh-CN-Yaoyao-Apollo` голоса. |
| `1.0.0-amd64-en-us-benjaminrus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-BenjaminRUS` голоса. |
| `1.0.0-amd64-en-us-guy24krus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-Guy24kRUS` голоса. |
| `1.0.0-amd64-en-us-jessa24krus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-Jessa24kRUS` голоса. |
| `1.0.0-amd64-en-us-jessarus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-JessaRUS` голоса. |
| `1.0.0-amd64-en-us-zirarus-preview` | Образ контейнера с языковым стандартом `en-US` и `en-US-ZiraRUS` голоса. |
| `1.0.0-amd64-zh-cn-huihuirus-preview` | Образ контейнера с языковым стандартом `zh-CN` и `zh-CN-HuihuiRUS` голоса. |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Образ контейнера с языковым стандартом `zh-CN` и `zh-CN-Kangkang-Apollo` голоса. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview` | Образ контейнера с языковым стандартом `zh-CN` и `zh-CN-Yaoyao-Apollo` голоса. |

## <a name="key-phrase-extraction"></a>Извлечение ключевой фразы

[Извлечение ключевых фраз][ta-kp] образ контейнера можно найти в разделе `mcr.microsoft.com` "синдикации реестра контейнеров". Он находится в репозитории `azure-cognitive-services` и называется `keyphrase`. Полное имя образа контейнера —, `mcr.microsoft.com/azure-cognitive-services/keyphrase`.

Этот образ контейнера имеет следующие доступные Теги:

| Теги изображений | Примечания |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

## <a name="language-detection"></a>Распознавание языка

[Распознавание языка][ta-la] образ контейнера можно найти в разделе `mcr.microsoft.com` "синдикации реестра контейнеров". Он находится в репозитории `azure-cognitive-services` и называется `language`. Полное имя образа контейнера —, `mcr.microsoft.com/azure-cognitive-services/language`.

Этот образ контейнера имеет следующие доступные Теги:

| Теги изображений | Примечания |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

## <a name="sentiment-analysis"></a>Анализ тональности

[Анализ тональности][ta-se] образ контейнера можно найти в разделе `mcr.microsoft.com` "синдикации реестра контейнеров". Он находится в репозитории `azure-cognitive-services` и называется `sentiment`. Полное имя образа контейнера —, `mcr.microsoft.com/azure-cognitive-services/sentiment`.

Этот образ контейнера имеет следующие доступные Теги:

| Теги изображений | Примечания |
|------------|:------|
| `latest` | |
| `1.1.009301-amd64-preview` | |
| `1.1.008510001-amd64-preview` | |
| `1.1.007750002-amd64-preview` | |
| `1.1.007360001-amd64-preview` | |
| `1.1.006770001-amd64-preview` | |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
