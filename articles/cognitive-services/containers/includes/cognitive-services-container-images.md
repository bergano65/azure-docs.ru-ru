---
title: Репозитории и образы контейнеров
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Две таблицы, представляющие реестры контейнеров, репозитории и имена образов для всех предложений службы.
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2019
ms.author: dapine
ms.openlocfilehash: c1593cb3dad7ee1370a66747fa3fe47e93c19957
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499154"
---
### <a name="container-repositories-and-images"></a>Репозитории и образы контейнеров

В таблицах ниже приведен полный список доступных образов контейнеров, предлагаемых Cognitive Services Azure.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Общедоступный "неусловный" (реестр контейнеров: `mcr.microsoft.com`)

В реестре контейнеров Microsoft размещены все общедоступные контейнеры с неконтролируемыми разворотами для Cognitive Services.

| служба | Контейнер | Реестр контейнеров, имя репозитория или образа |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Извлечение ключевой фразы | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Распознавание языка | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Анализ тональности | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Общедоступная Предварительная версия (реестр контейнеров: `containerpreview.azurecr.io`)

В реестре предварительного просмотра контейнера размещаются все общедоступные контейнеры с проверкой поCognitive Services. Эти контейнеры требуют формального запроса доступа, чтобы их можно было использовать.

| служба | Контейнер | Реестр контейнеров, имя репозитория или образа |
|--|--|--|
| [Детектор аномалий](../../anomaly-detector/anomaly-detector-container-howto.md) | Детектор аномалий | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Компьютерное зрение](../../Computer-vision/computer-vision-how-to-install-containers.md) | чтение | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Распознавание лиц](../../face/face-how-to-install-containers.md) | Распознавание лиц | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Распознаватель форм](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Распознаватель документов | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
< < < < < < < HEAD | [API речевой службы](../../speech-service/speech-container-howto.md?tab=stt) | Преобразование речи в текст | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` | | [API речевой службы](../../speech-service/speech-container-howto.md?tab=cstt) | Пользовательское распознавание речи в текст | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` | | [API речевой службы](../../speech-service/speech-container-howto.md?tab=tts) | Преобразование текста в речь | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` | | [API речевой службы](../../speech-service/speech-container-howto.md?tab=ctts) | Пользовательский текст в речь | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` | ======= | [API речевой службы](../../speech-service/speech-container-howto.md) | Преобразование речи в текст | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` | | [API речевой службы](../../speech-service/speech-container-howto.md) | Преобразование текста в речь | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` | | [Перевод текстов](../../translator/how-to-install-containers.md) | Перевод текстов | `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` |
>>>>>>> ReFS, remotes, MicrosoftDocs/Master
