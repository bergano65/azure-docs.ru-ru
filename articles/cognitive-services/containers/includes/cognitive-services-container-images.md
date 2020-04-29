---
title: Репозитории и образы контейнеров
services: cognitive-services
author: aahill
manager: nitinme
description: Две таблицы, представляющие реестры контейнеров, репозитории и имена образов для всех предложений службы.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a854a090af908da691e9b26f5b0714c6560fc0ba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876844"
---
### <a name="container-repositories-and-images"></a>Репозитории и образы контейнеров

Приведенные ниже таблицы представляют собой список доступных образов контейнеров, предлагаемых Azure Cognitive Services. Полный список доступных имен образов контейнеров и их доступных тегов см. в разделе [Cognitive Services Теги образа контейнера](../container-image-tags.md). В настоящее время отсутствуют общедоступные Cognitive Servicesные контейнеры. В течение этого времени, пока не сделаны дальнейшие объявления — контейнеры доступны в виде *общедоступного* или общедоступного *предварительного просмотра*.

 - *Общедоступная*неконтролируемая: контейнеры доступны в общедоступном виде без механизма ограничения.
 - *Общедоступная Предварительная версия*: контейнеры доступны в общедоступной версии, но сначала требуются формальный запрос на доступ к реестру контейнеров.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Открытый "неусловный" (реестр контейнеров: `mcr.microsoft.com`)

Реестр контейнеров (Майкрософт) (мкр) синдикации все общедоступные контейнеры с неконтролируемым доступом для Cognitive Services. Контейнеры также доступны непосредственно из [центра DOCKER](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Служба | Контейнер | Реестр контейнеров, имя репозитория или образа |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cogni'ive-services/luis` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Извлечение ключевых фраз | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Распознавание языка | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Анализ тональности | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Общедоступная Предварительная версия (реестр контейнеров: `containerpreview.azurecr.io`)

В реестре предварительного просмотра контейнера размещаются все общедоступные контейнеры с проверкой поCognitive Services. Для этих контейнеров необходим формальный запрос на доступ к ним через реестр контейнеров.

| Служба | Контейнер | Реестр контейнеров, имя репозитория или образа |
|--|--|--|
| [Детектор аномалий](../../anomaly-detector/anomaly-detector-container-howto.md) | Детектор аномалий | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [API Компьютерного зрения](../../Computer-vision/computer-vision-how-to-install-containers.md) | Чтение | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Распознавание лиц](../../face/face-how-to-install-containers.md) | Распознавание лиц | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Распознаватель форм](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Распознаватель документов | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=stt) | Преобразование речи в текст | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=cstt) | Пользовательское распознавание речи к тексту | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=tts) | Преобразование текста в речь | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=ctts) | Пользовательский текст в речь | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
