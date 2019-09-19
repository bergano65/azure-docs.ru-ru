---
title: Репозитории и образы контейнеров
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Две таблицы, представляющие реестры контейнеров, репозитории и имена образов для всех предложений службы.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/06/2019
ms.author: dapine
ms.openlocfilehash: 181f4acd86dfacb15592ded6f2df3287e3dc13bf
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130149"
---
### <a name="container-repositories-and-images"></a>Репозитории и образы контейнеров

В таблицах ниже приведен полный список доступных образов контейнеров, предлагаемых Cognitive Services Azure.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Открытый "неусловный" (реестр контейнеров: `mcr.microsoft.com`)

В реестре контейнеров Microsoft размещены все общедоступные контейнеры с неконтролируемыми разворотами для Cognitive Services.

| Служба | Контейнер | Реестр контейнеров, имя репозитория или образа |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Извлечение ключевых фраз | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Распознавание языка | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Анализ тональности | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Общедоступная Предварительная версия (реестр контейнеров: `containerpreview.azurecr.io`)

В реестре предварительного просмотра контейнера размещаются все общедоступные контейнеры с проверкой поCognitive Services. Эти контейнеры требуют формального запроса доступа, чтобы их можно было использовать.

| Служба | Контейнер | Реестр контейнеров, имя репозитория или образа |
|--|--|--|
| [Детектор аномалий](../../anomaly-detector/anomaly-detector-container-howto.md) | Детектор аномалий | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Компьютерное зрение](../../Computer-vision/computer-vision-how-to-install-containers.md) | Распознавание текста | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text` |
| [Компьютерное зрение](../../Computer-vision/computer-vision-how-to-install-containers.md) | прочтены | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Распознавание лиц](../../face/face-how-to-install-containers.md) | Распознавание лиц | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Распознаватель форм](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Распознаватель документов | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md) | Преобразование речи в текст | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md) | Преобразование текста в речь | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
