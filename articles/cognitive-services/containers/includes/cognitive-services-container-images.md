---
title: Контейнерные репозитории и изображения
services: cognitive-services
author: aahill
manager: nitinme
description: Две таблицы, представляющие реестры контейнеров, репозитории и имена изображений для всех предложений Cognitive Service.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a854a090af908da691e9b26f5b0714c6560fc0ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876844"
---
### <a name="container-repositories-and-images"></a>Контейнерные репозитории и изображения

Ниже приведен список доступных изображений контейнеров, предлагаемых Azure Cognitive Services. Полный список всех доступных имен изображений контейнера [Cognitive Services container image tags](../container-image-tags.md)и их доступных тегов см. В настоящее время, Есть нет когнитивных услуг контейнеров, которые, как правило, доступны (GA). В настоящее время, до тех пор, пока дальнейшие объявления сделаны - контейнеры доступны как *общественные ungated* или *общественных Gated Preview*.

 - *Публичное неустановленное*: контейнеры доступны публично без механизма gating.
 - *Public Gated Preview*: контейнеры доступны публично, но сначала требуют официального запроса на доступ к реестру контейнеров.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Публичный "Негосударственный" (контейнерный реестр: `mcr.microsoft.com`)

Реестр контейнеров Майкрософт (MCR) синдицирует все общедоступные «неугомные» контейнеры для когнитивных служб. Контейнеры также доступны непосредственно из [концентратора Docker.](https://hub.docker.com/_/microsoft-azure-cognitive-services)

| Служба | Контейнер | Реестр контейнеров / Репозиторий / Имя изображения |
|--|--|--|
| [Луис](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cogni'ive-services/luis` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Извлечение ключевых фраз | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Распознавание языка | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Анализ тональности | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Публичный "Gated" Предварительный `containerpreview.azurecr.io`просмотр (контейнерный реестр: )

Реестр просмотра контейнеров содержит все общедоступные "закрытые" контейнеры для когнитивных услуг. Эти контейнеры требуют официального запроса на доступ к ним через их контейнерный реестр.

| Служба | Контейнер | Реестр контейнеров / Репозиторий / Имя изображения |
|--|--|--|
| [Детектор аномалий](../../anomaly-detector/anomaly-detector-container-howto.md) | Детектор аномалий | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [API Компьютерного зрения](../../Computer-vision/computer-vision-how-to-install-containers.md) | Чтение | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Распознавание лиц](../../face/face-how-to-install-containers.md) | Распознавание лиц | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Распознаватель формы](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Распознаватель документов | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=stt) | Преобразование речи в текст | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=cstt) | Пользовательские речи к тексту | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=tts) | Преобразование текста в речь | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=ctts) | Пользовательский текст к речи | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
