---
title: Теги образа контейнера Cognitive Services
titleSuffix: Azure Cognitive Services
description: Полный список всех меток контейнера когнитивной службы.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: eed2223dbfeee307b552cdd010530f27c379f5fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219448"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Теги изображений контейнера Azure Cognitive Services

Azure Cognitive Services предлагает множество изображений контейнеров. Реестры контейнеров и соответствующие репозитории различаются между изображениями контейнеров. Каждое имя изображения контейнера предлагает несколько тегов. Тег изображения контейнера — это механизм версии изображения контейнера. Эта статья предназначена для использования в качестве всеобъемлющей ссылки для перечисления всех изображений контейнера Cognitive Services и доступных имеваемых тегов.

> [!TIP]
> При [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)использовании, обратите пристальное внимание на корпус контейнера реестра, репозиторий, имя изображения контейнера и соответствующие теги - как они **являются чувствительными.**

## <a name="anomaly-detector"></a>Детектор аномалий

Изображение контейнера [anomaly Detector][ad-containers] можно `containerpreview.azurecr.io` найти в реестре контейнеров. Он находится в `microsoft` репозитории `cognitive-services-anomaly-detector`и называется . Полностью квалифицированное имя `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`изображения контейнера, .

Это изображение контейнера имеет следующие теги доступны:

| Теги изображения                    | Примечания |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>API Компьютерного зрения

Изображение контейнера [Computer Vision][cv-containers] можно `containerpreview.azurecr.io` найти в реестре контейнеров. Он находится в `microsoft` репозитории `cognitive-services-read`и называется . Полностью квалифицированное имя `containerpreview.azurecr.io/microsoft/cognitive-services-read`изображения контейнера, .

Это изображение контейнера имеет следующие теги доступны:

| Теги изображения                    | Примечания |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Распознавание лиц

Изображение контейнера [Face][fa-containers] можно `containerpreview.azurecr.io` найти в реестре контейнеров. Он находится в `microsoft` репозитории `cognitive-services-face`и называется . Полностью квалифицированное имя `containerpreview.azurecr.io/microsoft/cognitive-services-face`изображения контейнера, .

Это изображение контейнера имеет следующие теги доступны:

| Теги изображения                    | Примечания |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |
| `1.1.006490002-amd64-preview` |       |
| `1.0.005940002-amd64-preview` |       |
| `1.0.005550001-amd64-preview` |       |

## <a name="form-recognizer"></a>Распознаватель документов

Изображение [контейнера распознавания форм][fr-containers] можно `containerpreview.azurecr.io` найти в реестре контейнеров. Он находится в `microsoft` репозитории `cognitive-services-form-recognizer`и называется . Полностью квалифицированное имя `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`изображения контейнера, .

Это изображение контейнера имеет следующие теги доступны:

| Теги изображения                    | Примечания |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Распознавание речи (LUIS)

Изображение контейнера [LUIS][lu-containers] можно `mcr.microsoft.com` найти на синдикате реестра контейнеров. Он находится в `azure-cognitive-services` репозитории `luis`и называется . Полностью квалифицированное имя `mcr.microsoft.com/azure-cognitive-services/luis`изображения контейнера, .

Это изображение контейнера имеет следующие теги доступны:

| Теги изображения                    | Примечания |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.010330004-amd64-preview` |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.008010002-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.007020001-amd64-preview` |       |

## <a name="custom-speech-to-text"></a>Пользовательские речи к тексту

Пользовательское изображение контейнера [между речевыми и текстами][sp-cstt] можно найти в реестре контейнеров. `containerpreview.azurecr.io` Он находится в `microsoft` репозитории `cognitive-services-custom-speech-to-text`и называется . Полностью квалифицированное имя `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`изображения контейнера, .

Это изображение контейнера имеет следующие теги доступны:

| Теги изображения            | Примечания |
|-----------------------|:------|
| `latest`              |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Пользовательский текст к речи

Пользовательское изображение контейнера [текст к речи][sp-ctts] `containerpreview.azurecr.io` можно найти в реестре контейнеров. Он находится в `microsoft` репозитории `cognitive-services-custom-text-to-speech`и называется . Полностью квалифицированное имя `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`изображения контейнера, .

Это изображение контейнера имеет следующие теги доступны:

| Теги изображения            | Примечания |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Преобразование речи в текст

Изображение контейнера [«Речи к тексту»][sp-stt] можно найти в реестре контейнеров. `containerpreview.azurecr.io` Он находится в `microsoft` репозитории `cognitive-services-speech-to-text`и называется . Полностью квалифицированное имя `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`изображения контейнера, .

Это изображение контейнера имеет следующие теги доступны:

| Теги изображения                  | Примечания                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Изображение контейнера `en-US` с локалью. |
| `2.1.1-amd64-ar-ae-preview` | Изображение контейнера `ar-AE` с локалью. |
| `2.1.1-amd64-ar-eg-preview` | Изображение контейнера `ar-EG` с локалью. |
| `2.1.1-amd64-ar-kw-preview` | Изображение контейнера `ar-KW` с локалью. |
| `2.1.1-amd64-ar-qa-preview` | Изображение контейнера `ar-QA` с локалью. |
| `2.1.1-amd64-ar-sa-preview` | Изображение контейнера `ar-SA` с локалью. |
| `2.1.1-amd64-ca-es-preview` | Изображение контейнера `ca-ES` с локалью. |
| `2.1.1-amd64-da-dk-preview` | Изображение контейнера `da-DK` с локалью. |
| `2.1.1-amd64-de-de-preview` | Изображение контейнера `de-DE` с локалью. |
| `2.1.1-amd64-en-au-preview` | Изображение контейнера `en-AU` с локалью. |
| `2.1.1-amd64-en-ca-preview` | Изображение контейнера `en-CA` с локалью. |
| `2.1.1-amd64-en-gb-preview` | Изображение контейнера `en-GB` с локалью. |
| `2.1.1-amd64-en-in-preview` | Изображение контейнера `en-IN` с локалью. |
| `2.1.1-amd64-en-nz-preview` | Изображение контейнера `en-NZ` с локалью. |
| `2.1.1-amd64-en-us-preview` | Изображение контейнера `en-US` с локалью. |
| `2.1.1-amd64-es-es-preview` | Изображение контейнера `es-ES` с локалью. |
| `2.1.1-amd64-es-mx-preview` | Изображение контейнера `es-MX` с локалью. |
| `2.1.1-amd64-fi-fi-preview` | Изображение контейнера `fi-FI` с локалью. |
| `2.1.1-amd64-fr-ca-preview` | Изображение контейнера `fr-CA` с локалью. |
| `2.1.1-amd64-fr-fr-preview` | Изображение контейнера `fr-FR` с локалью. |
| `2.1.1-amd64-gu-in-preview` | Изображение контейнера `gu-IN` с локалью. |
| `2.1.1-amd64-hi-in-preview` | Изображение контейнера `hi-IN` с локалью. |
| `2.1.1-amd64-it-it-preview` | Изображение контейнера `it-IT` с локалью. |
| `2.1.1-amd64-ja-jp-preview` | Изображение контейнера `ja-JP` с локалью. |
| `2.1.1-amd64-ko-kr-preview` | Изображение контейнера `ko-KR` с локалью. |
| `2.1.1-amd64-mr-in-preview` | Изображение контейнера `mr-IN` с локалью. |
| `2.1.1-amd64-nb-no-preview` | Изображение контейнера `nb-NO` с локалью. |
| `2.1.1-amd64-nl-nl-preview` | Изображение контейнера `nl-NL` с локалью. |
| `2.1.1-amd64-pl-pl-preview` | Изображение контейнера `pl-PL` с локалью. |
| `2.1.1-amd64-pt-br-preview` | Изображение контейнера `pt-BR` с локалью. |
| `2.1.1-amd64-pt-pt-preview` | Изображение контейнера `pt-PT` с локалью. |
| `2.1.1-amd64-ru-ru-preview` | Изображение контейнера `ru-RU` с локалью. |
| `2.1.1-amd64-sv-se-preview` | Изображение контейнера `sv-SE` с локалью. |
| `2.1.1-amd64-ta-in-preview` | Изображение контейнера `ta-IN` с локалью. |
| `2.1.1-amd64-te-in-preview` | Изображение контейнера `te-IN` с локалью. |
| `2.1.1-amd64-th-th-preview` | Изображение контейнера `th-TH` с локалью. |
| `2.1.1-amd64-tr-tr-preview` | Изображение контейнера `tr-TR` с локалью. |
| `2.1.1-amd64-zh-cn-preview` | Изображение контейнера `zh-CN` с локалью. |
| `2.1.1-amd64-zh-hk-preview` | Изображение контейнера `zh-HK` с локалью. |
| `2.1.1-amd64-zh-tw-preview` | Изображение контейнера `zh-TW` с локалью. |
| `2.1.0-amd64-ar-ae-preview` | Изображение контейнера `ar-AE` с локалью. |
| `2.1.0-amd64-ar-eg-preview` | Изображение контейнера `ar-EG` с локалью. |
| `2.1.0-amd64-ar-kw-preview` | Изображение контейнера `ar-KW` с локалью. |
| `2.1.0-amd64-ar-qa-preview` | Изображение контейнера `ar-QA` с локалью. |
| `2.1.0-amd64-ar-sa-preview` | Изображение контейнера `ar-SA` с локалью. |
| `2.1.0-amd64-ca-es-preview` | Изображение контейнера `ca-ES` с локалью. |
| `2.1.0-amd64-da-dk-preview` | Изображение контейнера `da-DK` с локалью. |
| `2.1.0-amd64-de-de-preview` | Изображение контейнера `de-DE` с локалью. |
| `2.1.0-amd64-en-au-preview` | Изображение контейнера `en-AU` с локалью. |
| `2.1.0-amd64-en-ca-preview` | Изображение контейнера `en-CA` с локалью. |
| `2.1.0-amd64-en-gb-preview` | Изображение контейнера `en-GB` с локалью. |
| `2.1.0-amd64-en-in-preview` | Изображение контейнера `en-IN` с локалью. |
| `2.1.0-amd64-en-nz-preview` | Изображение контейнера `en-NZ` с локалью. |
| `2.1.0-amd64-en-us-preview` | Изображение контейнера `en-US` с локалью. |
| `2.1.0-amd64-es-es-preview` | Изображение контейнера `es-ES` с локалью. |
| `2.1.0-amd64-es-mx-preview` | Изображение контейнера `es-MX` с локалью. |
| `2.1.0-amd64-fi-fi-preview` | Изображение контейнера `fi-FI` с локалью. |
| `2.1.0-amd64-fr-ca-preview` | Изображение контейнера `fr-CA` с локалью. |
| `2.1.0-amd64-fr-fr-preview` | Изображение контейнера `fr-FR` с локалью. |
| `2.1.0-amd64-gu-in-preview` | Изображение контейнера `gu-IN` с локалью. |
| `2.1.0-amd64-hi-in-preview` | Изображение контейнера `hi-IN` с локалью. |
| `2.1.0-amd64-it-it-preview` | Изображение контейнера `it-IT` с локалью. |
| `2.1.0-amd64-ja-jp-preview` | Изображение контейнера `ja-JP` с локалью. |
| `2.1.0-amd64-ko-kr-preview` | Изображение контейнера `ko-KR` с локалью. |
| `2.1.0-amd64-mr-in-preview` | Изображение контейнера `mr-IN` с локалью. |
| `2.1.0-amd64-nb-no-preview` | Изображение контейнера `nb-NO` с локалью. |
| `2.1.0-amd64-nl-nl-preview` | Изображение контейнера `nl-NL` с локалью. |
| `2.1.0-amd64-pl-pl-preview` | Изображение контейнера `pl-PL` с локалью. |
| `2.1.0-amd64-pt-br-preview` | Изображение контейнера `pt-BR` с локалью. |
| `2.1.0-amd64-pt-pt-preview` | Изображение контейнера `pt-PT` с локалью. |
| `2.1.0-amd64-ru-ru-preview` | Изображение контейнера `ru-RU` с локалью. |
| `2.1.0-amd64-sv-se-preview` | Изображение контейнера `sv-SE` с локалью. |
| `2.1.0-amd64-ta-in-preview` | Изображение контейнера `ta-IN` с локалью. |
| `2.1.0-amd64-te-in-preview` | Изображение контейнера `te-IN` с локалью. |
| `2.1.0-amd64-th-th-preview` | Изображение контейнера `th-TH` с локалью. |
| `2.1.0-amd64-tr-tr-preview` | Изображение контейнера `tr-TR` с локалью. |
| `2.1.0-amd64-zh-cn-preview` | Изображение контейнера `zh-CN` с локалью. |
| `2.1.0-amd64-zh-hk-preview` | Изображение контейнера `zh-HK` с локалью. |
| `2.1.0-amd64-zh-tw-preview` | Изображение контейнера `zh-TW` с локалью. |
| `2.0.3-amd64-ja-jp-preview` | Изображение контейнера `ja-JP` с локалью. |
| `2.0.2-amd64-ar-ae-preview` | Изображение контейнера `ar-AE` с локалью. |
| `2.0.2-amd64-ar-eg-preview` | Изображение контейнера `ar-EG` с локалью. |
| `2.0.2-amd64-ar-kw-preview` | Изображение контейнера `ar-KW` с локалью. |
| `2.0.2-amd64-ar-qa-preview` | Изображение контейнера `ar-QA` с локалью. |
| `2.0.2-amd64-ar-sa-preview` | Изображение контейнера `ar-SA` с локалью. |
| `2.0.2-amd64-ca-es-preview` | Изображение контейнера `ca-ES` с локалью. |
| `2.0.2-amd64-da-dk-preview` | Изображение контейнера `da-DK` с локалью. |
| `2.0.2-amd64-de-de-preview` | Изображение контейнера `de-DE` с локалью. |
| `2.0.2-amd64-en-au-preview` | Изображение контейнера `en-AU` с локалью. |
| `2.0.2-amd64-en-ca-preview` | Изображение контейнера `en-CA` с локалью. |
| `2.0.2-amd64-en-gb-preview` | Изображение контейнера `en-GB` с локалью. |
| `2.0.2-amd64-en-in-preview` | Изображение контейнера `en-IN` с локалью. |
| `2.0.2-amd64-en-nz-preview` | Изображение контейнера `en-NZ` с локалью. |
| `2.0.2-amd64-en-us-preview` | Изображение контейнера `en-US` с локалью. |
| `2.0.2-amd64-es-es-preview` | Изображение контейнера `es-ES` с локалью. |
| `2.0.2-amd64-es-mx-preview` | Изображение контейнера `es-MX` с локалью. |
| `2.0.2-amd64-fi-fi-preview` | Изображение контейнера `fi-FI` с локалью. |
| `2.0.2-amd64-fr-ca-preview` | Изображение контейнера `fr-CA` с локалью. |
| `2.0.2-amd64-fr-fr-preview` | Изображение контейнера `fr-FR` с локалью. |
| `2.0.2-amd64-gu-in-preview` | Изображение контейнера `gu-IN` с локалью. |
| `2.0.2-amd64-hi-in-preview` | Изображение контейнера `hi-IN` с локалью. |
| `2.0.2-amd64-it-it-preview` | Изображение контейнера `it-IT` с локалью. |
| `2.0.2-amd64-ja-jp-preview` | Изображение контейнера `ja-JP` с локалью. |
| `2.0.2-amd64-ko-kr-preview` | Изображение контейнера `ko-KR` с локалью. |
| `2.0.2-amd64-mr-in-preview` | Изображение контейнера `mr-IN` с локалью. |
| `2.0.2-amd64-nb-no-preview` | Изображение контейнера `nb-NO` с локалью. |
| `2.0.2-amd64-nl-nl-preview` | Изображение контейнера `nl-NL` с локалью. |
| `2.0.2-amd64-pl-pl-preview` | Изображение контейнера `pl-PL` с локалью. |
| `2.0.2-amd64-pt-br-preview` | Изображение контейнера `pt-BR` с локалью. |
| `2.0.2-amd64-pt-pt-preview` | Изображение контейнера `pt-PT` с локалью. |
| `2.0.2-amd64-ru-ru-preview` | Изображение контейнера `ru-RU` с локалью. |
| `2.0.2-amd64-sv-se-preview` | Изображение контейнера `sv-SE` с локалью. |
| `2.0.2-amd64-ta-in-preview` | Изображение контейнера `ta-IN` с локалью. |
| `2.0.2-amd64-te-in-preview` | Изображение контейнера `te-IN` с локалью. |
| `2.0.2-amd64-th-th-preview` | Изображение контейнера `th-TH` с локалью. |
| `2.0.2-amd64-tr-tr-preview` | Изображение контейнера `tr-TR` с локалью. |
| `2.0.2-amd64-zh-cn-preview` | Изображение контейнера `zh-CN` с локалью. |
| `2.0.2-amd64-zh-hk-preview` | Изображение контейнера `zh-HK` с локалью. |
| `2.0.2-amd64-zh-tw-preview` | Изображение контейнера `zh-TW` с локалью. |
| `2.0.1-amd64-ar-ae-preview` | Изображение контейнера `ar-AE` с локалью. |
| `2.0.1-amd64-ar-eg-preview` | Изображение контейнера `ar-EG` с локалью. |
| `2.0.1-amd64-ar-kw-preview` | Изображение контейнера `ar-KW` с локалью. |
| `2.0.1-amd64-ar-qa-preview` | Изображение контейнера `ar-QA` с локалью. |
| `2.0.1-amd64-ar-sa-preview` | Изображение контейнера `ar-SA` с локалью. |
| `2.0.1-amd64-ca-es-preview` | Изображение контейнера `ca-ES` с локалью. |
| `2.0.1-amd64-da-dk-preview` | Изображение контейнера `da-DK` с локалью. |
| `2.0.1-amd64-de-de-preview` | Изображение контейнера `de-DE` с локалью. |
| `2.0.1-amd64-en-au-preview` | Изображение контейнера `en-AU` с локалью. |
| `2.0.1-amd64-en-ca-preview` | Изображение контейнера `en-CA` с локалью. |
| `2.0.1-amd64-en-gb-preview` | Изображение контейнера `en-GB` с локалью. |
| `2.0.1-amd64-en-in-preview` | Изображение контейнера `en-IN` с локалью. |
| `2.0.1-amd64-en-nz-preview` | Изображение контейнера `en-NZ` с локалью. |
| `2.0.1-amd64-en-us-preview` | Изображение контейнера `en-US` с локалью. |
| `2.0.1-amd64-es-es-preview` | Изображение контейнера `es-ES` с локалью. |
| `2.0.1-amd64-es-mx-preview` | Изображение контейнера `es-MX` с локалью. |
| `2.0.1-amd64-fi-fi-preview` | Изображение контейнера `fi-FI` с локалью. |
| `2.0.1-amd64-fr-ca-preview` | Изображение контейнера `fr-CA` с локалью. |
| `2.0.1-amd64-fr-fr-preview` | Изображение контейнера `fr-FR` с локалью. |
| `2.0.1-amd64-gu-in-preview` | Изображение контейнера `gu-IN` с локалью. |
| `2.0.1-amd64-hi-in-preview` | Изображение контейнера `hi-IN` с локалью. |
| `2.0.1-amd64-it-it-preview` | Изображение контейнера `it-IT` с локалью. |
| `2.0.1-amd64-ja-jp-preview` | Изображение контейнера `ja-JP` с локалью. |
| `2.0.1-amd64-ko-kr-preview` | Изображение контейнера `ko-KR` с локалью. |
| `2.0.1-amd64-mr-in-preview` | Изображение контейнера `mr-IN` с локалью. |
| `2.0.1-amd64-nb-no-preview` | Изображение контейнера `nb-NO` с локалью. |
| `2.0.1-amd64-nl-nl-preview` | Изображение контейнера `nl-NL` с локалью. |
| `2.0.1-amd64-pl-pl-preview` | Изображение контейнера `pl-PL` с локалью. |
| `2.0.1-amd64-pt-br-preview` | Изображение контейнера `pt-BR` с локалью. |
| `2.0.1-amd64-pt-pt-preview` | Изображение контейнера `pt-PT` с локалью. |
| `2.0.1-amd64-ru-ru-preview` | Изображение контейнера `ru-RU` с локалью. |
| `2.0.1-amd64-sv-se-preview` | Изображение контейнера `sv-SE` с локалью. |
| `2.0.1-amd64-ta-in-preview` | Изображение контейнера `ta-IN` с локалью. |
| `2.0.1-amd64-te-in-preview` | Изображение контейнера `te-IN` с локалью. |
| `2.0.1-amd64-th-th-preview` | Изображение контейнера `th-TH` с локалью. |
| `2.0.1-amd64-tr-tr-preview` | Изображение контейнера `tr-TR` с локалью. |
| `2.0.1-amd64-zh-cn-preview` | Изображение контейнера `zh-CN` с локалью. |
| `2.0.1-amd64-zh-hk-preview` | Изображение контейнера `zh-HK` с локалью. |
| `2.0.1-amd64-zh-tw-preview` | Изображение контейнера `zh-TW` с локалью. |
| `2.0.0-amd64-ar-eg-preview` | Изображение контейнера `ar-EG` с локалью. |
| `2.0.0-amd64-ca-es-preview` | Изображение контейнера `ca-ES` с локалью. |
| `2.0.0-amd64-da-dk-preview` | Изображение контейнера `da-DK` с локалью. |
| `2.0.0-amd64-de-de-preview` | Изображение контейнера `de-DE` с локалью. |
| `2.0.0-amd64-en-au-preview` | Изображение контейнера `en-AU` с локалью. |
| `2.0.0-amd64-en-ca-preview` | Изображение контейнера `en-CA` с локалью. |
| `2.0.0-amd64-en-gb-preview` | Изображение контейнера `en-GB` с локалью. |
| `2.0.0-amd64-en-in-preview` | Изображение контейнера `en-IN` с локалью. |
| `2.0.0-amd64-en-nz-preview` | Изображение контейнера `en-NZ` с локалью. |
| `2.0.0-amd64-en-us-preview` | Изображение контейнера `en-US` с локалью. |
| `2.0.0-amd64-es-es-preview` | Изображение контейнера `es-ES` с локалью. |
| `2.0.0-amd64-es-mx-preview` | Изображение контейнера `es-MX` с локалью. |
| `2.0.0-amd64-fi-fi-preview` | Изображение контейнера `fi-FI` с локалью. |
| `2.0.0-amd64-fr-ca-preview` | Изображение контейнера `fr-CA` с локалью. |
| `2.0.0-amd64-fr-fr-preview` | Изображение контейнера `fr-FR` с локалью. |
| `2.0.0-amd64-hi-in-preview` | Изображение контейнера `hi-IN` с локалью. |
| `2.0.0-amd64-it-it-preview` | Изображение контейнера `it-IT` с локалью. |
| `2.0.0-amd64-ja-jp-preview` | Изображение контейнера `ja-JP` с локалью. |
| `2.0.0-amd64-ko-kr-preview` | Изображение контейнера `ko-KR` с локалью. |
| `2.0.0-amd64-nb-no-preview` | Изображение контейнера `nb-NO` с локалью. |
| `2.0.0-amd64-nl-nl-preview` | Изображение контейнера `nl-NL` с локалью. |
| `2.0.0-amd64-pl-pl-preview` | Изображение контейнера `pl-PL` с локалью. |
| `2.0.0-amd64-pt-br-preview` | Изображение контейнера `pt-BR` с локалью. |
| `2.0.0-amd64-pt-pt-preview` | Изображение контейнера `pt-PT` с локалью. |
| `2.0.0-amd64-ru-ru-preview` | Изображение контейнера `ru-RU` с локалью. |
| `2.0.0-amd64-sv-se-preview` | Изображение контейнера `sv-SE` с локалью. |
| `2.0.0-amd64-th-th-preview` | Изображение контейнера `th-TH` с локалью. |
| `2.0.0-amd64-tr-tr-preview` | Изображение контейнера `tr-TR` с локалью. |
| `2.0.0-amd64-zh-cn-preview` | Изображение контейнера `zh-CN` с локалью. |
| `2.0.0-amd64-zh-hk-preview` | Изображение контейнера `zh-HK` с локалью. |
| `2.0.0-amd64-zh-tw-preview` | Изображение контейнера `zh-TW` с локалью. |
| `1.2.0-amd64-de-de-preview` | Изображение контейнера `de-DE` с локалью. |
| `1.2.0-amd64-en-au-preview` | Изображение контейнера `en-AU` с локалью. |
| `1.2.0-amd64-en-ca-preview` | Изображение контейнера `en-CA` с локалью. |
| `1.2.0-amd64-en-gb-preview` | Изображение контейнера `en-GB` с локалью. |
| `1.2.0-amd64-en-in-preview` | Изображение контейнера `en-IN` с локалью. |
| `1.2.0-amd64-en-us-preview` | Изображение контейнера `en-US` с локалью. |
| `1.2.0-amd64-es-es-preview` | Изображение контейнера `es-ES` с локалью. |
| `1.2.0-amd64-es-mx-preview` | Изображение контейнера `es-MX` с локалью. |
| `1.2.0-amd64-fr-ca-preview` | Изображение контейнера `fr-CA` с локалью. |
| `1.2.0-amd64-fr-fr-preview` | Изображение контейнера `fr-FR` с локалью. |
| `1.2.0-amd64-it-it-preview` | Изображение контейнера `it-IT` с локалью. |
| `1.2.0-amd64-ja-jp-preview` | Изображение контейнера `ja-JP` с локалью. |
| `1.2.0-amd64-pt-br-preview` | Изображение контейнера `pt-BR` с локалью. |
| `1.2.0-amd64-zh-cn-preview` | Изображение контейнера `zh-CN` с локалью. |
| `1.1.3-amd64-de-de-preview` | Изображение контейнера `de-DE` с локалью. |
| `1.1.3-amd64-en-au-preview` | Изображение контейнера `en-AU` с локалью. |
| `1.1.3-amd64-en-ca-preview` | Изображение контейнера `en-CA` с локалью. |
| `1.1.3-amd64-en-gb-preview` | Изображение контейнера `en-GB` с локалью. |
| `1.1.3-amd64-en-in-preview` | Изображение контейнера `en-IN` с локалью. |
| `1.1.3-amd64-en-us-preview` | Изображение контейнера `en-US` с локалью. |
| `1.1.3-amd64-es-es-preview` | Изображение контейнера `es-ES` с локалью. |
| `1.1.3-amd64-es-mx-preview` | Изображение контейнера `es-MX` с локалью. |
| `1.1.3-amd64-fr-ca-preview` | Изображение контейнера `fr-CA` с локалью. |
| `1.1.3-amd64-fr-fr-preview` | Изображение контейнера `fr-FR` с локалью. |
| `1.1.3-amd64-it-it-preview` | Изображение контейнера `it-IT` с локалью. |
| `1.1.3-amd64-ja-jp-preview` | Изображение контейнера `ja-JP` с локалью. |
| `1.1.3-amd64-pt-br-preview` | Изображение контейнера `pt-BR` с локалью. |
| `1.1.3-amd64-zh-cn-preview` | Изображение контейнера `zh-CN` с локалью. |
| `1.1.2-amd64-de-de-preview` | Изображение контейнера `de-DE` с локалью. |
| `1.1.2-amd64-en-au-preview` | Изображение контейнера `en-AU` с локалью. |
| `1.1.2-amd64-en-ca-preview` | Изображение контейнера `en-CA` с локалью. |
| `1.1.2-amd64-en-gb-preview` | Изображение контейнера `en-GB` с локалью. |
| `1.1.2-amd64-en-in-preview` | Изображение контейнера `en-IN` с локалью. |
| `1.1.2-amd64-en-us-preview` | Изображение контейнера `en-US` с локалью. |
| `1.1.2-amd64-es-es-preview` | Изображение контейнера `es-ES` с локалью. |
| `1.1.2-amd64-es-mx-preview` | Изображение контейнера `es-MX` с локалью. |
| `1.1.2-amd64-fr-ca-preview` | Изображение контейнера `fr-CA` с локалью. |
| `1.1.2-amd64-fr-fr-preview` | Изображение контейнера `fr-FR` с локалью. |
| `1.1.2-amd64-it-it-preview` | Изображение контейнера `it-IT` с локалью. |
| `1.1.2-amd64-ja-jp-preview` | Изображение контейнера `ja-JP` с локалью. |
| `1.1.2-amd64-pt-br-preview` | Изображение контейнера `pt-BR` с локалью. |
| `1.1.2-amd64-zh-cn-preview` | Изображение контейнера `zh-CN` с локалью. |
| `1.1.1-amd64-de-de-preview` | Изображение контейнера `de-DE` с локалью. |
| `1.1.1-amd64-en-au-preview` | Изображение контейнера `en-AU` с локалью. |
| `1.1.1-amd64-en-ca-preview` | Изображение контейнера `en-CA` с локалью. |
| `1.1.1-amd64-en-gb-preview` | Изображение контейнера `en-GB` с локалью. |
| `1.1.1-amd64-en-in-preview` | Изображение контейнера `en-IN` с локалью. |
| `1.1.1-amd64-en-us-preview` | Изображение контейнера `en-US` с локалью. |
| `1.1.1-amd64-es-es-preview` | Изображение контейнера `es-ES` с локалью. |
| `1.1.1-amd64-es-mx-preview` | Изображение контейнера `es-MX` с локалью. |
| `1.1.1-amd64-fr-ca-preview` | Изображение контейнера `fr-CA` с локалью. |
| `1.1.1-amd64-fr-fr-preview` | Изображение контейнера `fr-FR` с локалью. |
| `1.1.1-amd64-it-it-preview` | Изображение контейнера `it-IT` с локалью. |
| `1.1.1-amd64-ja-jp-preview` | Изображение контейнера `ja-JP` с локалью. |
| `1.1.1-amd64-pt-br-preview` | Изображение контейнера `pt-BR` с локалью. |
| `1.1.1-amd64-zh-cn-preview` | Изображение контейнера `zh-CN` с локалью. |
| `1.1.0-amd64-de-de-preview` | Изображение контейнера `de-DE` с локалью. |
| `1.1.0-amd64-en-au-preview` | Изображение контейнера `en-AU` с локалью. |
| `1.1.0-amd64-en-ca-preview` | Изображение контейнера `en-CA` с локалью. |
| `1.1.0-amd64-en-gb-preview` | Изображение контейнера `en-GB` с локалью. |
| `1.1.0-amd64-en-in-preview` | Изображение контейнера `en-IN` с локалью. |
| `1.1.0-amd64-en-us-preview` | Изображение контейнера `en-US` с локалью. |
| `1.1.0-amd64-es-es-preview` | Изображение контейнера `es-ES` с локалью. |
| `1.1.0-amd64-es-mx-preview` | Изображение контейнера `es-MX` с локалью. |
| `1.1.0-amd64-fr-ca-preview` | Изображение контейнера `fr-CA` с локалью. |
| `1.1.0-amd64-fr-fr-preview` | Изображение контейнера `fr-FR` с локалью. |
| `1.1.0-amd64-it-it-preview` | Изображение контейнера `it-IT` с локалью. |
| `1.1.0-amd64-ja-jp-preview` | Изображение контейнера `ja-JP` с локалью. |
| `1.1.0-amd64-pt-br-preview` | Изображение контейнера `pt-BR` с локалью. |
| `1.1.0-amd64-zh-cn-preview` | Изображение контейнера `zh-CN` с локалью. |
| `1.0.0-amd64-de-de-preview` | Изображение контейнера `de-DE` с локалью. |
| `1.0.0-amd64-en-au-preview` | Изображение контейнера `en-AU` с локалью. |
| `1.0.0-amd64-en-ca-preview` | Изображение контейнера `en-CA` с локалью. |
| `1.0.0-amd64-en-gb-preview` | Изображение контейнера `en-GB` с локалью. |
| `1.0.0-amd64-en-in-preview` | Изображение контейнера `en-IN` с локалью. |
| `1.0.0-amd64-en-us-preview` | Изображение контейнера `en-US` с локалью. |
| `1.0.0-amd64-es-es-preview` | Изображение контейнера `es-ES` с локалью. |
| `1.0.0-amd64-es-mx-preview` | Изображение контейнера `es-MX` с локалью. |
| `1.0.0-amd64-fr-ca-preview` | Изображение контейнера `fr-CA` с локалью. |
| `1.0.0-amd64-fr-fr-preview` | Изображение контейнера `fr-FR` с локалью. |
| `1.0.0-amd64-it-it-preview` | Изображение контейнера `it-IT` с локалью. |
| `1.0.0-amd64-ja-jp-preview` | Изображение контейнера `ja-JP` с локалью. |
| `1.0.0-amd64-pt-br-preview` | Изображение контейнера `pt-BR` с локалью. |
| `1.0.0-amd64-zh-cn-preview` | Изображение контейнера `zh-CN` с локалью. |

## <a name="text-to-speech"></a>Преобразование текста в речь

Изображение контейнера [«Текст к речи»][sp-tts] `containerpreview.azurecr.io` можно найти в реестре контейнеров. Он находится в `microsoft` репозитории `cognitive-services-text-to-speech`и называется . Полностью квалифицированное имя `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`изображения контейнера, .

Это изображение контейнера имеет следующие теги доступны:

| Теги изображения                                  | Примечания                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Изображение контейнера `en-US` с `en-US-JessaRUS` локализом и голосом.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Изображение контейнера `ar-EG` с `ar-EG-Hoda` локализом и голосом.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Изображение контейнера `ar-SA` с `ar-SA-Naayf` локализом и голосом.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Изображение контейнера `bg-BG` с `bg-BG-Ivan` локализом и голосом.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Изображение контейнера `ca-ES` с `ca-ES-HerenaRUS` локализом и голосом.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Изображение контейнера `cs-CZ` с `cs-CZ-Jakub` локализом и голосом.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Изображение контейнера `da-DK` с `da-DK-HelleRUS` локализом и голосом.        |
| `1.3.0-amd64-de-at-michael-preview`         | Изображение контейнера `de-AT` с `de-AT-Michael` локализом и голосом.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Изображение контейнера `de-CH` с `de-CH-Karsten` локализом и голосом.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Изображение контейнера `de-DE` с `de-DE-Hedda` локализом и голосом.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Изображение контейнера `de-DE` с `de-DE-Hedda` локализом и голосом.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Изображение контейнера `de-DE` с `de-DE-HeddaRUS` локализом и голосом.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Изображение контейнера `de-DE` с `de-DE-Stefan-Apollo` локализом и голосом.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Изображение контейнера `el-GR` с `el-GR-Stefanos` локализом и голосом.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Изображение контейнера `en-AU` с `en-AU-Catherine` локализом и голосом.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Изображение контейнера `en-AU` с `en-AU-HayleyRUS` локализом и голосом.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Изображение контейнера `en-CA` с `en-CA-HeatherRUS` локализом и голосом.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Изображение контейнера `en-CA` с `en-CA-Linda` локализом и голосом.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Изображение контейнера `en-GB` с `en-GB-George-Apollo` локализом и голосом.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Изображение контейнера `en-GB` с `en-GB-HazelRUS` локализом и голосом.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Изображение контейнера `en-GB` с `en-GB-Susan-Apollo` локализом и голосом.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Изображение контейнера `en-IE` с `en-IE-Sean` локализом и голосом.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Изображение контейнера `en-IN` с `en-IN-Heera-Apollo` локализом и голосом.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Изображение контейнера `en-IN` с `en-IN-PriyaRUS` локализом и голосом.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Изображение контейнера `en-IN` с `en-IN-Ravi-Apollo` локализом и голосом.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Изображение контейнера `en-US` с `en-US-BenjaminRUS` локализом и голосом.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Изображение контейнера `en-US` с `en-US-Guy24kRUS` локализом и голосом.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Изображение контейнера `en-US` с `en-US-Jessa24kRUS` локализом и голосом.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Изображение контейнера `en-US` с `en-US-JessaRUS` локализом и голосом.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Изображение контейнера `en-US` с `en-US-ZiraRUS` локализом и голосом.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Изображение контейнера `es-ES` с `es-ES-HelenaRUS` локализом и голосом.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Изображение контейнера `es-ES` с `es-ES-Laura-Apollo` локализом и голосом.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Изображение контейнера `es-ES` с `es-ES-Pablo-Apollo` локализом и голосом.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Изображение контейнера `es-MX` с `es-MX-HildaRUS` локализом и голосом.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Изображение контейнера `es-MX` с `es-MX-Raul-Apollo` локализом и голосом.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Изображение контейнера `fi-FI` с `fi-FI-HeidiRUS` локализом и голосом.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Изображение контейнера `fr-CA` с `fr-CA-Caroline` локализом и голосом.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Изображение контейнера `fr-CA` с `fr-CA-HarmonieRUS` локализом и голосом.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Изображение контейнера `fr-CH` с `fr-CH-Guillaume` локализом и голосом.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Изображение контейнера `fr-FR` с `fr-FR-HortenseRUS` локализом и голосом.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Изображение контейнера `fr-FR` с `fr-FR-Julie-Apollo` локализом и голосом.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Изображение контейнера `fr-FR` с `fr-FR-Paul-Apollo` локализом и голосом.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Изображение контейнера `he-IL` с `he-IL-Asaf` локализом и голосом.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Изображение контейнера `hi-IN` с `hi-IN-Hemant` локализом и голосом.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Изображение контейнера `hi-IN` с `hi-IN-Kalpana-Apollo` локализом и голосом.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Изображение контейнера `hi-IN` с `hi-IN-Kalpana` локализом и голосом.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Изображение контейнера `hi-IN` с `hi-IN-Kalpana` локализом и голосом.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Изображение контейнера `hr-HR` с `hr-HR-Matej` локализом и голосом.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Изображение контейнера `hu-HU` с `hu-HU-Szabolcs` локализом и голосом.        |
| `1.3.0-amd64-id-id-andika-preview`          | Изображение контейнера `id-ID` с `id-ID-Andika` локализом и голосом.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Изображение контейнера `it-IT` с `it-IT-Cosimo-Apollo` локализом и голосом.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Изображение контейнера `it-IT` с `it-IT-LuciaRUS` локализом и голосом.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Изображение контейнера `ja-JP` с `ja-JP-Ayumi-Apollo` локализом и голосом.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Изображение контейнера `ja-JP` с `ja-JP-HarukaRUS` локализом и голосом.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Изображение контейнера `ja-JP` с `ja-JP-Ichiro-Apollo` локализом и голосом.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Изображение контейнера `ko-KR` с `ko-KR-HeamiRUS` локализом и голосом.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Изображение контейнера `ms-MY` с `ms-MY-Rizwan` локализом и голосом.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Изображение контейнера `nb-NO` с `nb-NO-HuldaRUS` локализом и голосом.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Изображение контейнера `nl-NL` с `nl-NL-HannaRUS` локализом и голосом.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Изображение контейнера `pl-PL` с `pl-PL-PaulinaRUS` локализом и голосом.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Изображение контейнера `pt-BR` с `pt-BR-Daniel-Apollo` локализом и голосом.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Изображение контейнера `pt-BR` с `pt-BR-HeloisaRUS` локализом и голосом.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Изображение контейнера `pt-PT` с `pt-PT-HeliaRUS` локализом и голосом.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Изображение контейнера `ro-RO` с `ro-RO-Andrei` локализом и голосом.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Изображение контейнера `ru-RU` с `ru-RU-EkaterinaRUS` локализом и голосом.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Изображение контейнера `ru-RU` с `ru-RU-Irina-Apollo` локализом и голосом.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Изображение контейнера `ru-RU` с `ru-RU-Pavel-Apollo` локализом и голосом.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Изображение контейнера `sk-SK` с `sk-SK-Filip` локализом и голосом.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Изображение контейнера `sl-SI` с `sl-SI-Lado` локализом и голосом.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Изображение контейнера `sv-SE` с `sv-SE-HedvigRUS` локализом и голосом.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Изображение контейнера `ta-IN` с `ta-IN-Valluvar` локализом и голосом.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Изображение контейнера `te-IN` с `te-IN-Chitra` локализом и голосом.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Изображение контейнера `th-TH` с `th-TH-Pattara` локализом и голосом.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Изображение контейнера `tr-TR` с `tr-TR-SedaRUS` локализом и голосом.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Изображение контейнера `vi-VN` с `vi-VN-An` локализом и голосом.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Изображение контейнера `zh-CN` с `zh-CN-HuihuiRUS` локализом и голосом.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Изображение контейнера `zh-CN` с `zh-CN-Kangkang-Apollo` локализом и голосом. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Изображение контейнера `zh-CN` с `zh-CN-Yaoyao-Apollo` локализом и голосом.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Изображение контейнера `zh-HK` с `zh-HK-Danny-Apollo` локализом и голосом.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Изображение контейнера `zh-HK` с `zh-HK-Tracy-Apollo` локализом и голосом.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Изображение контейнера `zh-HK` с `zh-HK-TracyRUS` локализом и голосом.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Изображение контейнера `zh-TW` с `zh-TW-HanHanRUS` локализом и голосом.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Изображение контейнера `zh-TW` с `zh-TW-Yating-Apollo` локализом и голосом.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Изображение контейнера `zh-TW` с `zh-TW-Zhiwei-Apollo` локализом и голосом.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Изображение контейнера `de-DE` с `de-DE-Hedda` локализом и голосом.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Изображение контейнера `de-DE` с `de-DE-HeddaRUS` локализом и голосом.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Изображение контейнера `de-DE` с `de-DE-Stefan-Apollo` локализом и голосом.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Изображение контейнера `en-AU` с `en-AU-Catherine` локализом и голосом.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Изображение контейнера `en-AU` с `en-AU-HayleyRUS` локализом и голосом.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Изображение контейнера `en-GB` с `en-GB-George-Apollo` локализом и голосом.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Изображение контейнера `en-GB` с `en-GB-HazelRUS` локализом и голосом.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Изображение контейнера `en-GB` с `en-GB-Susan-Apollo` локализом и голосом.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Изображение контейнера `en-IN` с `en-IN-Heera-Apollo` локализом и голосом.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Изображение контейнера `en-IN` с `en-IN-PriyaRUS` локализом и голосом.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Изображение контейнера `en-IN` с `en-IN-Ravi-Apollo` локализом и голосом.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Изображение контейнера `en-US` с `en-US-BenjaminRUS` локализом и голосом.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Изображение контейнера `en-US` с `en-US-Guy24kRUS` локализом и голосом.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Изображение контейнера `en-US` с `en-US-Jessa24kRUS` локализом и голосом.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Изображение контейнера `en-US` с `en-US-JessaRUS` локализом и голосом.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Изображение контейнера `en-US` с `en-US-ZiraRUS` локализом и голосом.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Изображение контейнера `es-ES` с `es-ES-HelenaRUS` локализом и голосом.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Изображение контейнера `es-ES` с `es-ES-Laura-Apollo` локализом и голосом.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Изображение контейнера `es-ES` с `es-ES-Pablo-Apollo` локализом и голосом.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Изображение контейнера `es-MX` с `es-MX-HildaRUS` локализом и голосом.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Изображение контейнера `es-MX` с `es-MX-Raul-Apollo` локализом и голосом.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Изображение контейнера `fr-CA` с `fr-CA-Caroline` локализом и голосом.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Изображение контейнера `fr-CA` с `fr-CA-HarmonieRUS` локализом и голосом.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Изображение контейнера `fr-FR` с `fr-FR-HortenseRUS` локализом и голосом.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Изображение контейнера `fr-FR` с `fr-FR-Julie-Apollo` локализом и голосом.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Изображение контейнера `fr-FR` с `fr-FR-Paul-Apollo` локализом и голосом.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Изображение контейнера `it-IT` с `it-IT-Cosimo-Apollo` локализом и голосом.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Изображение контейнера `it-IT` с `it-IT-LuciaRUS` локализом и голосом.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Изображение контейнера `ja-JP` с `ja-JP-Ayumi-Apollo` локализом и голосом.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Изображение контейнера `ja-JP` с `ja-JP-HarukaRUS` локализом и голосом.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Изображение контейнера `ja-JP` с `ja-JP-Ichiro-Apollo` локализом и голосом.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Изображение контейнера `ko-KR` с `ko-KR-HeamiRUS` локализом и голосом.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Изображение контейнера `pt-BR` с `pt-BR-Daniel-Apollo` локализом и голосом.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Изображение контейнера `pt-BR` с `pt-BR-HeloisaRUS` локализом и голосом.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Изображение контейнера `zh-CN` с `zh-CN-HuihuiRUS` локализом и голосом.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Изображение контейнера `zh-CN` с `zh-CN-Kangkang-Apollo` локализом и голосом. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Изображение контейнера `zh-CN` с `zh-CN-Yaoyao-Apollo` локализом и голосом.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Изображение контейнера `de-DE` с `de-DE-Hedda` локализом и голосом.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Изображение контейнера `de-DE` с `de-DE-Hedda` локализом и голосом.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Изображение контейнера `de-DE` с `de-DE-HeddaRUS` локализом и голосом.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Изображение контейнера `de-DE` с `de-DE-Stefan-Apollo` локализом и голосом.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Изображение контейнера `en-AU` с `en-AU-Catherine` локализом и голосом.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Изображение контейнера `en-AU` с `en-AU-HayleyRUS` локализом и голосом.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Изображение контейнера `en-GB` с `en-GB-George-Apollo` локализом и голосом.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Изображение контейнера `en-GB` с `en-GB-HazelRUS` локализом и голосом.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Изображение контейнера `en-GB` с `en-GB-Susan-Apollo` локализом и голосом.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Изображение контейнера `en-IN` с `en-IN-Heera-Apollo` локализом и голосом.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Изображение контейнера `en-IN` с `en-IN-PriyaRUS` локализом и голосом.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Изображение контейнера `en-IN` с `en-IN-Ravi-Apollo` локализом и голосом.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Изображение контейнера `en-US` с `en-US-BenjaminRUS` локализом и голосом.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Изображение контейнера `en-US` с `en-US-Guy24kRUS` локализом и голосом.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Изображение контейнера `en-US` с `en-US-Jessa24kRUS` локализом и голосом.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Изображение контейнера `en-US` с `en-US-JessaRUS` локализом и голосом.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Изображение контейнера `en-US` с `en-US-ZiraRUS` локализом и голосом.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Изображение контейнера `es-ES` с `es-ES-HelenaRUS` локализом и голосом.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Изображение контейнера `es-ES` с `es-ES-Laura-Apollo` локализом и голосом.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Изображение контейнера `es-ES` с `es-ES-Pablo-Apollo` локализом и голосом.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Изображение контейнера `es-MX` с `es-MX-HildaRUS` локализом и голосом.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Изображение контейнера `es-MX` с `es-MX-Raul-Apollo` локализом и голосом.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Изображение контейнера `fr-CA` с `fr-CA-Caroline` локализом и голосом.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Изображение контейнера `fr-CA` с `fr-CA-HarmonieRUS` локализом и голосом.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Изображение контейнера `fr-FR` с `fr-FR-HortenseRUS` локализом и голосом.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Изображение контейнера `fr-FR` с `fr-FR-Julie-Apollo` локализом и голосом.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Изображение контейнера `fr-FR` с `fr-FR-Paul-Apollo` локализом и голосом.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Изображение контейнера `it-IT` с `it-IT-Cosimo-Apollo` локализом и голосом.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Изображение контейнера `it-IT` с `it-IT-LuciaRUS` локализом и голосом.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Изображение контейнера `ja-JP` с `ja-JP-Ayumi-Apollo` локализом и голосом.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Изображение контейнера `ja-JP` с `ja-JP-HarukaRUS` локализом и голосом.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Изображение контейнера `ja-JP` с `ja-JP-Ichiro-Apollo` локализом и голосом.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Изображение контейнера `ko-KR` с `ko-KR-HeamiRUS` локализом и голосом.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Изображение контейнера `pt-BR` с `pt-BR-Daniel-Apollo` локализом и голосом.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Изображение контейнера `pt-BR` с `pt-BR-HeloisaRUS` локализом и голосом.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Изображение контейнера `zh-CN` с `zh-CN-HuihuiRUS` локализом и голосом.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Изображение контейнера `zh-CN` с `zh-CN-Kangkang-Apollo` локализом и голосом. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Изображение контейнера `zh-CN` с `zh-CN-Yaoyao-Apollo` локализом и голосом.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Изображение контейнера `en-US` с `en-US-BenjaminRUS` локализом и голосом.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Изображение контейнера `en-US` с `en-US-Guy24kRUS` локализом и голосом.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Изображение контейнера `en-US` с `en-US-Jessa24kRUS` локализом и голосом.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Изображение контейнера `en-US` с `en-US-JessaRUS` локализом и голосом.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Изображение контейнера `en-US` с `en-US-ZiraRUS` локализом и голосом.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Изображение контейнера `zh-CN` с `zh-CN-HuihuiRUS` локализом и голосом.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Изображение контейнера `zh-CN` с `zh-CN-Kangkang-Apollo` локализом и голосом. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Изображение контейнера `zh-CN` с `zh-CN-Yaoyao-Apollo` локализом и голосом.   |

## <a name="key-phrase-extraction"></a>Извлечение ключевых фраз

Изображение контейнера [Key Phrase Extraction][ta-kp] `mcr.microsoft.com` можно найти на синдикате реестра контейнеров. Он находится в `azure-cognitive-services` репозитории `keyphrase`и называется . Полностью квалифицированное имя `mcr.microsoft.com/azure-cognitive-services/keyphrase`изображения контейнера, .

Это изображение контейнера имеет следующие теги доступны:

| Теги изображения                    | Примечания |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Распознавание языка

Изображение контейнера [Language Detection][ta-la] можно `mcr.microsoft.com` найти на синдикате реестра контейнеров. Он находится в `azure-cognitive-services` репозитории `language`и называется . Полностью квалифицированное имя `mcr.microsoft.com/azure-cognitive-services/language`изображения контейнера, .

Это изображение контейнера имеет следующие теги доступны:

| Теги изображения                    | Примечания |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Анализ тональности

Образ контейнера [анализа настроений][ta-se] `mcr.microsoft.com` можно найти на синдикате реестра контейнеров. Он находится в `azure-cognitive-services` репозитории `sentiment`и называется . Полностью квалифицированное имя `mcr.microsoft.com/azure-cognitive-services/sentiment`изображения контейнера, .

Это изображение контейнера имеет следующие теги доступны:

| Теги изображения                    | Примечания |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

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
