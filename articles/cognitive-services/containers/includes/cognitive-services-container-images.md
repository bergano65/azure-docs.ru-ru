---
title: Репозитории и образы контейнеров
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Две таблицы, представляющие реестры контейнеров, репозитории и имена образов для всех предложений службы.
ms.service: cognitive-services
ms.topic: include
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 2058dd6e52ddb417e24368b27384df9a222c378e
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74142216"
---
### <a name="container-repositories-and-images"></a>Репозитории и образы контейнеров

Приведенные ниже таблицы представляют собой список доступных образов контейнеров, предлагаемых Azure Cognitive Services. Полный список доступных имен образов контейнеров и их доступных тегов см. в разделе [Cognitive Services Теги образа контейнера](../container-image-tags.md).

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Общедоступный "неусловный" (реестр контейнеров: `mcr.microsoft.com`)

Реестр контейнеров (Майкрософт) (мкр) синдикации все общедоступные контейнеры с неконтролируемым доступом для Cognitive Services. Они также доступны непосредственно из [центра DOCKER](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Служба | Контейнер | Реестр контейнеров, имя репозитория или образа |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Извлечение ключевой фразы | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Распознавание языка | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Анализ тональности | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Общедоступная Предварительная версия (реестр контейнеров: `containerpreview.azurecr.io`)

В реестре предварительного просмотра контейнера размещаются все общедоступные контейнеры с проверкой поCognitive Services. Эти контейнеры требуют формального запроса доступа, чтобы их можно было использовать.

| Служба | Контейнер | Реестр контейнеров, имя репозитория или образа |
|--|--|--|
| [Детектор аномалий](../../anomaly-detector/anomaly-detector-container-howto.md) | Детектор аномалий | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Компьютерное зрение](../../Computer-vision/computer-vision-how-to-install-containers.md) | чтение | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Распознавание лиц](../../face/face-how-to-install-containers.md) | API распознавания лиц | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Распознаватель форм](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Распознаватель документов | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=stt) | Преобразование речи в текст. | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=cstt) | Пользовательское распознавание речи к тексту | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=tts) | Преобразование текста в речь | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=ctts) | Пользовательский текст в речь | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
