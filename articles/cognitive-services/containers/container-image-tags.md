---
title: Теги Cognitive Servicesного образа контейнера
titleSuffix: Azure Cognitive Services
description: Полный список тегов образа контейнера службы.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 8a8c49089d5c0554c4636e98b5820ef206010207
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134181"
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
| `2.0.0-amd64-zh-tw-preview` | |
| `2.0.0-amd64-zh-hk-preview` | |
| `2.0.0-amd64-zh-cn-preview` | |
| `2.0.0-amd64-tr-tr-preview` | |
| `2.0.0-amd64-th-th-preview` | |
| `2.0.0-amd64-sv-se-preview` | |
| `2.0.0-amd64-ru-ru-preview` | |
| `2.0.0-amd64-pt-pt-preview` | |
| `2.0.0-amd64-pt-br-preview` | |
| `2.0.0-amd64-pl-pl-preview` | |
| `2.0.0-amd64-nl-nl-preview` | |
| `2.0.0-amd64-nb-no-preview` | |
| `2.0.0-amd64-ko-kr-preview` | |
| `2.0.0-amd64-ja-jp-preview` | |
| `2.0.0-amd64-it-it-preview` | |
| `2.0.0-amd64-hi-in-preview` | |
| `2.0.0-amd64-fr-fr-preview` | |
| `2.0.0-amd64-fr-ca-preview` | |
| `2.0.0-amd64-fi-fi-preview` | |
| `2.0.0-amd64-es-mx-preview` | |
| `2.0.0-amd64-es-es-preview` | |
| `2.0.0-amd64-en-us-preview` | |
| `2.0.0-amd64-en-nz-preview` | |
| `2.0.0-amd64-en-in-preview` | |
| `2.0.0-amd64-en-gb-preview` | |
| `2.0.0-amd64-en-ca-preview` | |
| `2.0.0-amd64-en-au-preview` | |
| `2.0.0-amd64-de-de-preview` | |
| `2.0.0-amd64-da-dk-preview` | |
| `2.0.0-amd64-ca-es-preview` | |
| `2.0.0-amd64-ar-eg-preview` | |
| `1.2.0-amd64-zh-cn-preview` | |
| `1.2.0-amd64-pt-br-preview` | |
| `1.2.0-amd64-preview` | |
| `1.2.0-amd64-ja-jp-preview` | |
| `1.2.0-amd64-it-it-preview` | |
| `1.2.0-amd64-fr-fr-preview` | |
| `1.2.0-amd64-fr-ca-preview` | |
| `1.2.0-amd64-es-mx-preview` | |
| `1.2.0-amd64-es-es-preview` | |
| `1.2.0-amd64-en-us-preview` | |
| `1.2.0-amd64-en-in-preview` | |
| `1.2.0-amd64-en-gb-preview` | |
| `1.2.0-amd64-en-ca-preview` | |
| `1.2.0-amd64-en-au-preview` | |
| `1.2.0-amd64-de-de-preview` | |
| `1.1.3-amd64-zh-cn-preview` | |
| `1.1.3-amd64-pt-br-preview` | |
| `1.1.3-amd64-preview` | |
| `1.1.3-amd64-ja-jp-preview` | |
| `1.1.3-amd64-it-it-preview` | |
| `1.1.3-amd64-fr-fr-preview` | |
| `1.1.3-amd64-fr-ca-preview` | |
| `1.1.3-amd64-es-mx-preview` | |
| `1.1.3-amd64-es-es-preview` | |
| `1.1.3-amd64-en-us-preview` | |
| `1.1.3-amd64-en-in-preview` | |
| `1.1.3-amd64-en-gb-preview` | |
| `1.1.3-amd64-en-ca-preview` | |
| `1.1.3-amd64-en-au-preview` | |
| `1.1.3-amd64-de-de-preview` | |
| `1.1.2-amd64-zh-cn-preview` | |
| `1.1.2-amd64-pt-br-preview` | |
| `1.1.2-amd64-preview` | |
| `1.1.2-amd64-ja-jp-preview` | |
| `1.1.2-amd64-it-it-preview` | |
| `1.1.2-amd64-fr-fr-preview` | |
| `1.1.2-amd64-fr-ca-preview` | |
| `1.1.2-amd64-es-mx-preview` | |
| `1.1.2-amd64-es-es-preview` | |
| `1.1.2-amd64-en-us-preview` | |
| `1.1.2-amd64-en-in-preview` | |
| `1.1.2-amd64-en-gb-preview` | |
| `1.1.2-amd64-en-ca-preview` | |
| `1.1.2-amd64-en-au-preview` | |
| `1.1.2-amd64-de-de-preview` | |
| `1.1.1-amd64-zh-cn-preview` | |
| `1.1.1-amd64-pt-br-preview` | |
| `1.1.1-amd64-ja-jp-preview` | |
| `1.1.1-amd64-it-it-preview` | |
| `1.1.1-amd64-fr-fr-preview` | |
| `1.1.1-amd64-fr-ca-preview` | |
| `1.1.1-amd64-es-mx-preview` | |
| `1.1.1-amd64-es-es-preview` | |
| `1.1.1-amd64-en-us-preview` | |
| `1.1.1-amd64-en-in-preview` | |
| `1.1.1-amd64-en-gb-preview` | |
| `1.1.1-amd64-en-ca-preview` | |
| `1.1.1-amd64-en-au-preview` | |
| `1.1.1-amd64-de-de-preview` | |
| `1.1.0-amd64-zh-cn-preview` | |
| `1.1.0-amd64-pt-br-preview` | |
| `1.1.0-amd64-ja-jp-preview` | |
| `1.1.0-amd64-it-it-preview` | |
| `1.1.0-amd64-fr-fr-preview` | |
| `1.1.0-amd64-fr-ca-preview` | |
| `1.1.0-amd64-es-mx-preview` | |
| `1.1.0-amd64-es-es-preview` | |
| `1.1.0-amd64-en-us-preview` | |
| `1.1.0-amd64-en-in-preview` | |
| `1.1.0-amd64-en-gb-preview` | |
| `1.1.0-amd64-en-ca-preview` | |
| `1.1.0-amd64-en-au-preview` | |
| `1.1.0-amd64-de-de-preview` | |
| `1.0.0-amd64-zh-cn-preview` | |
| `1.0.0-amd64-pt-br-preview` | |
| `1.0.0-amd64-ja-jp-preview` | |
| `1.0.0-amd64-it-it-preview` | |
| `1.0.0-amd64-fr-fr-preview` | |
| `1.0.0-amd64-fr-ca-preview` | |
| `1.0.0-amd64-es-mx-preview` | |
| `1.0.0-amd64-es-es-preview` | |
| `1.0.0-amd64-en-us-preview` | |
| `1.0.0-amd64-en-in-preview` | |
| `1.0.0-amd64-en-gb-preview` | |
| `1.0.0-amd64-en-ca-preview` | |
| `1.0.0-amd64-en-au-preview` | |
| `1.0.0-amd64-de-de-preview` | |

## <a name="text-to-speech"></a>Преобразование текста в речь

Образ контейнера преобразования [текста в речь][sp-tts] можно найти в реестре контейнеров `containerpreview.azurecr.io`. Он находится в репозитории `microsoft` и называется `cognitive-services-text-to-speech`. Полное имя образа контейнера —, `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`.

Этот образ контейнера имеет следующие доступные Теги:

| Теги изображений | Примечания |
|------------|:------|
| `latest` | Образ контейнера с языковым стандартом `en-US` и `JessaRUS` голоса. |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview` | |
| `1.3.0-amd64-zh-tw-yating-apollo-preview` | |
| `1.3.0-amd64-zh-tw-hanhanrus-preview` | |
| `1.3.0-amd64-zh-hk-tracyrus-preview` | |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview` | |
| `1.3.0-amd64-zh-hk-danny-apollo-preview` | |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview` | |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | |
| `1.3.0-amd64-zh-cn-huihuirus-preview` | |
| `1.3.0-amd64-vi-vn-an-preview` | |
| `1.3.0-amd64-tr-tr-sedarus-preview` | |
| `1.3.0-amd64-th-th-pattara-preview` | |
| `1.3.0-amd64-te-in-chitra-preview` | |
| `1.3.0-amd64-ta-in-valluvar-preview` | |
| `1.3.0-amd64-sv-se-hedvigrus-preview` | |
| `1.3.0-amd64-sl-si-lado-preview` | |
| `1.3.0-amd64-sk-sk-filip-preview` | |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview` | |
| `1.3.0-amd64-ru-ru-irina-apollo-preview` | |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview` | |
| `1.3.0-amd64-ro-ro-andrei-preview` | |
| `1.3.0-amd64-pt-pt-heliarus-preview` | |
| `1.3.0-amd64-pt-br-heloisarus-preview` | |
| `1.3.0-amd64-pt-br-daniel-apollo-preview` | |
| `1.3.0-amd64-pl-pl-paulinarus-preview` | |
| `1.3.0-amd64-nl-nl-hannarus-preview` | |
| `1.3.0-amd64-nb-no-huldarus-preview` | |
| `1.3.0-amd64-ms-my-rizwan-preview` | |
| `1.3.0-amd64-ko-kr-heamirus-preview` | |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview` | |
| `1.3.0-amd64-ja-jp-harukarus-preview` | |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview` | |
| `1.3.0-amd64-it-it-luciarus-preview` | |
| `1.3.0-amd64-it-it-cosimo-apollo-preview` | |
| `1.3.0-amd64-id-id-andika-preview` | |
| `1.3.0-amd64-hu-hu-szabolcs-preview` | |
| `1.3.0-amd64-hr-hr-matej-preview` | |
| `1.3.0-amd64-hi-in-kalpana-preview` | |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview` | |
| `1.3.0-amd64-hi-in-hemant-preview` | |
| `1.3.0-amd64-he-il-asaf-preview` | |
| `1.3.0-amd64-fr-fr-paul-apollo-preview` | |
| `1.3.0-amd64-fr-fr-julie-apollo-preview` | |
| `1.3.0-amd64-fr-fr-hortenserus-preview` | |
| `1.3.0-amd64-fr-ch-guillaume-preview` | |
| `1.3.0-amd64-fr-ca-harmonierus-preview` | |
| `1.3.0-amd64-fr-ca-caroline-preview` | |
| `1.3.0-amd64-fi-fi-heidirus-preview` | |
| `1.3.0-amd64-es-mx-raul-apollo-preview` | |
| `1.3.0-amd64-es-mx-hildarus-preview` | |
| `1.3.0-amd64-es-es-pablo-apollo-preview` | |
| `1.3.0-amd64-es-es-laura-apollo-preview` | |
| `1.3.0-amd64-es-es-helenarus-preview` | |
| `1.3.0-amd64-en-us-zirarus-preview` | |
| `1.3.0-amd64-en-us-jessarus-preview` | |
| `1.3.0-amd64-en-us-jessa24krus-preview` | |
| `1.3.0-amd64-en-us-guy24krus-preview` | |
| `1.3.0-amd64-en-us-benjaminrus-preview` | |
| `1.3.0-amd64-en-in-ravi-apollo-preview` | |
| `1.3.0-amd64-en-in-priyarus-preview` | |
| `1.3.0-amd64-en-in-heera-apollo-preview` | |
| `1.3.0-amd64-en-ie-sean-preview` | |
| `1.3.0-amd64-en-gb-susan-apollo-preview` | |
| `1.3.0-amd64-en-gb-hazelrus-preview` | |
| `1.3.0-amd64-en-gb-george-apollo-preview` | |
| `1.3.0-amd64-en-ca-linda-preview` | |
| `1.3.0-amd64-en-ca-heatherrus-preview` | |
| `1.3.0-amd64-en-au-hayleyrus-preview` | |
| `1.3.0-amd64-en-au-catherine-preview` | |
| `1.3.0-amd64-el-gr-stefanos-preview` | |
| `1.3.0-amd64-de-de-stefan-apollo-preview` | |
| `1.3.0-amd64-de-de-heddarus-preview` | |
| `1.3.0-amd64-de-de-hedda-preview` | |
| `1.3.0-amd64-de-ch-karsten-preview` | |
| `1.3.0-amd64-de-at-michael-preview` | |
| `1.3.0-amd64-da-dk-hellerus-preview` | |
| `1.3.0-amd64-cs-cz-jakub-preview` | |
| `1.3.0-amd64-ca-es-herenarus-preview` | |
| `1.3.0-amd64-bg-bg-ivan-preview` | |
| `1.3.0-amd64-ar-sa-naayf-preview` | |
| `1.3.0-amd64-ar-eg-hoda-preview` | |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview` | |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | |
| `1.2.0-amd64-zh-cn-huihuirus-preview` | |
| `1.2.0-amd64-pt-br-heloisarus-preview` | |
| `1.2.0-amd64-pt-br-daniel-apollo-preview` | |
| `1.2.0-amd64-ko-kr-heamirus-preview` | |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview` | |
| `1.2.0-amd64-ja-jp-harukarus-preview` | |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview` | |
| `1.2.0-amd64-it-it-luciarus-preview` | |
| `1.2.0-amd64-it-it-cosimo-apollo-preview` | |
| `1.2.0-amd64-fr-fr-paul-apollo-preview` | |
| `1.2.0-amd64-fr-fr-julie-apollo-preview` | |
| `1.2.0-amd64-fr-fr-hortenserus-preview` | |
| `1.2.0-amd64-fr-ca-harmonierus-preview` | |
| `1.2.0-amd64-fr-ca-caroline-preview` | |
| `1.2.0-amd64-es-mx-raul-apollo-preview` | |
| `1.2.0-amd64-es-mx-hildarus-preview` | |
| `1.2.0-amd64-es-es-pablo-apollo-preview` | |
| `1.2.0-amd64-es-es-laura-apollo-preview` | |
| `1.2.0-amd64-es-es-helenarus-preview` | |
| `1.2.0-amd64-en-us-zirarus-preview` | |
| `1.2.0-amd64-en-us-jessarus-preview` | |
| `1.2.0-amd64-en-us-jessa24krus-preview` | |
| `1.2.0-amd64-en-us-guy24krus-preview` | |
| `1.2.0-amd64-en-us-benjaminrus-preview` | |
| `1.2.0-amd64-en-in-ravi-apollo-preview` | |
| `1.2.0-amd64-en-in-priyarus-preview` | |
| `1.2.0-amd64-en-in-heera-apollo-preview` | |
| `1.2.0-amd64-en-gb-susan-apollo-preview` | |
| `1.2.0-amd64-en-gb-hazelrus-preview` | |
| `1.2.0-amd64-en-gb-george-apollo-preview` | |
| `1.2.0-amd64-en-au-hayleyrus-preview` | |
| `1.2.0-amd64-en-au-catherine-preview` | |
| `1.2.0-amd64-de-de-stefan-apollo-preview` | |
| `1.2.0-amd64-de-de-heddarus-preview` | |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview` | |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | |
| `1.1.0-amd64-zh-cn-huihuirus-preview` | |
| `1.1.0-amd64-pt-br-heloisarus-preview` | |
| `1.1.0-amd64-pt-br-daniel-apollo-preview` | |
| `1.1.0-amd64-ko-kr-heamirus-preview` | |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview` | |
| `1.1.0-amd64-ja-jp-harukarus-preview` | |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview` | |
| `1.1.0-amd64-it-it-luciarus-preview` | |
| `1.1.0-amd64-it-it-cosimo-apollo-preview` | |
| `1.1.0-amd64-fr-fr-paul-apollo-preview` | |
| `1.1.0-amd64-fr-fr-julie-apollo-preview` | |
| `1.1.0-amd64-fr-fr-hortenserus-preview` | |
| `1.1.0-amd64-fr-ca-harmonierus-preview` | |
| `1.1.0-amd64-fr-ca-caroline-preview` | |
| `1.1.0-amd64-es-mx-raul-apollo-preview` | |
| `1.1.0-amd64-es-mx-hildarus-preview` | |
| `1.1.0-amd64-es-es-pablo-apollo-preview` | |
| `1.1.0-amd64-es-es-laura-apollo-preview` | |
| `1.1.0-amd64-es-es-helenarus-preview` | |
| `1.1.0-amd64-en-us-zirarus-preview` | |
| `1.1.0-amd64-en-us-jessarus-preview` | |
| `1.1.0-amd64-en-us-jessa24krus-preview` | |
| `1.1.0-amd64-en-us-guy24krus-preview` | |
| `1.1.0-amd64-en-us-benjaminrus-preview` | |
| `1.1.0-amd64-en-in-ravi-apollo-preview` | |
| `1.1.0-amd64-en-in-priyarus-preview` | |
| `1.1.0-amd64-en-in-heera-apollo-preview` | |
| `1.1.0-amd64-en-gb-susan-apollo-preview` | |
| `1.1.0-amd64-en-gb-hazelrus-preview` | |
| `1.1.0-amd64-en-gb-george-apollo-preview` | |
| `1.1.0-amd64-en-au-hayleyrus-preview` | |
| `1.1.0-amd64-en-au-catherine-preview` | |
| `1.1.0-amd64-de-de-stefan-apollo-preview` | |
| `1.1.0-amd64-de-de-heddarus-preview` | |
| `1.1.0-amd64-de-de-hedda-preview` | |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview` | |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | |
| `1.0.0-amd64-zh-cn-huihuirus-preview` | |
| `1.0.0-amd64-en-us-zirarus-preview` | |
| `1.0.0-amd64-en-us-jessarus-preview` | |
| `1.0.0-amd64-en-us-jessa24krus-preview` | |
| `1.0.0-amd64-en-us-guy24krus-preview` | |
| `1.0.0-amd64-en-us-benjaminrus-preview` | |

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
