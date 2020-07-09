---
title: Репозитории и образы контейнеров
services: cognitive-services
author: aahill
manager: nitinme
description: Две таблицы, представляющие реестры контейнеров, репозитории и имена образов для всех предложений службы.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: ac8e49c85147576e489b16a3a421c75d60ad4aee
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86104421"
---
### <a name="container-repositories-and-images"></a>Репозитории и образы контейнеров

Приведенные ниже таблицы представляют собой список доступных образов контейнеров, предлагаемых Azure Cognitive Services. Полный список доступных имен образов контейнеров и их доступных тегов см. в разделе [Cognitive Services Теги образа контейнера](../container-image-tags.md). 

#### <a name="generally-available"></a>Общедоступная версия 

Реестр контейнеров (Майкрософт) (мкр) выйдет все общедоступные контейнеры для Cognitive Services. Контейнеры также доступны непосредственно из [центра DOCKER](https://hub.docker.com/_/microsoft-azure-cognitive-services).

#### <a name="luis"></a>[LUIS](#tab/luis)

| Контейнер LUIS | Реестр контейнеров, имя репозитория или образа |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |

Дополнительные сведения см. [в разделе Запуск и установка контейнеров Luis](../../LUIS/luis-container-howto.md) .

#### <a name="text-analytics"></a>[Анализ текста](#tab/text-analytics)

| Контейнер Анализ текста | Реестр контейнеров, имя репозитория или образа |
|--|--|
| Анализ тональности v3 (английский) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en` |
| Анализ тональности v3 (Испанский) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-es` |
| Анализ тональности v3 (французский) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-fr` |
| Анализ тональности v3 (Итальянский) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-it` |
| Анализ тональности v3 (немецкий) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-de` |
| Анализ тональности v3 (упрощенное письмо) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zh` |
| Анализ тональности v3 (китайский (традиционное письмо)) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zht` |
| Анализ тональности v3 (японский) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-ja` |
| Анализ тональности v3 (Португальский) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-pt` |
| Анализ тональности v3 (Голландский) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-nl` |

Дополнительные сведения см. [в разделе Запуск и установка контейнеров анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) .

---

#### <a name="public-ungated-preview-container-registry-mcrmicrosoftcom"></a>Общедоступная Предварительная версия (реестр контейнеров: `mcr.microsoft.com` )

Следующие предварительные версии контейнеров доступны в общем доступе. В реестре контейнеров (Майкрософт) (мкр) опубликованы все общедоступные неусловные контейнеры для Cognitive Services. Контейнеры также доступны непосредственно из [центра DOCKER](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Служба | Контейнер | Реестр контейнеров, имя репозитория или образа |
|--|--|--|
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Извлечение ключевых фраз | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Распознавание языка | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Детектор аномалий](../../anomaly-detector/anomaly-detector-container-howto.md) | Детектор аномалий | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Общедоступная Предварительная версия (реестр контейнеров: `containerpreview.azurecr.io` )

Следующие контейнеры с условным просмотром размещаются в реестре предварительной версии контейнера и должны иметь доступ к приложению. Дополнительные сведения см. в следующих статьях о контейнерах.

| Служба | Контейнер | Реестр контейнеров, имя репозитория или образа |
|--|--|--|
| [Компьютерное зрение](../../Computer-vision/computer-vision-how-to-install-containers.md) | Чтение | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Распознавание лиц](../../face/face-how-to-install-containers.md) | Распознавание лиц | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Распознаватель форм](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Распознаватель документов | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=stt) | Преобразование речи в текст | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=cstt) | Пользовательское распознавание речи к тексту | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=tts) | Преобразование текста в речь | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=ctts) | Пользовательский текст в речь | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
| [Анализ текста для работоспособности](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Анализ текста для работоспособности | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |
