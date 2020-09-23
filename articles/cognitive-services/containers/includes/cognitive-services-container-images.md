---
title: Репозитории и образы контейнеров
services: cognitive-services
author: aahill
manager: nitinme
description: Две таблицы, представляющие реестры контейнеров, репозитории и имена образов для всех предложений службы.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: d514460f3cd80b5b85604251743abbbbcd1adc8c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907010"
---
### <a name="container-repositories-and-images"></a>Репозитории и образы контейнеров

Приведенные ниже таблицы представляют собой список доступных образов контейнеров, предлагаемых Azure Cognitive Services. Полный список доступных имен образов контейнеров и их доступных тегов см. в разделе [Cognitive Services Теги образа контейнера](../container-image-tags.md). 

#### <a name="generally-available"></a>Общедоступная версия 

Реестр контейнеров (Майкрософт) (мкр) выйдет все общедоступные контейнеры для Cognitive Services. Контейнеры также доступны непосредственно из [центра DOCKER](https://hub.docker.com/_/microsoft-azure-cognitive-services).

**LUIS**

| Контейнер | Реестр контейнеров, имя репозитория или образа |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/language/luis` |

Дополнительные сведения см. [в разделе Запуск и установка контейнеров Luis](../../LUIS/luis-container-howto.md) .

**Анализ текста**

| Контейнер | Реестр контейнеров, имя репозитория или образа |
|--|--|
| Анализ тональности v3 (английский) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en` |
| Анализ тональности v3 (Испанский) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-es` |
| Анализ тональности v3 (французский) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-fr` |
| Анализ тональности v3 (Итальянский) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-it` |
| Анализ тональности v3 (немецкий) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-de` |
| Анализ тональности v3 (упрощенное письмо) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zh` |
| Анализ тональности v3 (китайский (традиционное письмо)) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zht` |
| Анализ тональности v3 (японский) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-ja` |
| Анализ тональности v3 (Португальский) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-pt` |
| Анализ тональности v3 (Голландский) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-nl` |

Дополнительные сведения см. [в разделе Запуск и установка контейнеров анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) .

**Детектор аномалий** 

| Контейнер | Реестр контейнеров, имя репозитория или образа |
|--|--|
| Детектор аномалий; | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` |

Дополнительные сведения см. [в статье запуск и установка контейнеров детекторов аномалий](../../anomaly-detector/anomaly-detector-container-howto.md) .

**Служба речи**

> [!NOTE]
> Чтобы использовать речевые контейнеры, необходимо выполнить [форму запроса в Интернете](https://aka.ms/csgate).

| Контейнер | Реестр контейнеров, имя репозитория или образа |
|--|--|
| [Преобразование речи в текст](../../speech-service/speech-container-howto.md?tab=stt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` |
| [Пользовательское распознавание речи к тексту](../../speech-service/speech-container-howto.md?tab=cstt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` |
| [Преобразование текста в речь](../../speech-service/speech-container-howto.md?tab=tts) | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` |

#### <a name="ungated-preview"></a>Предварительный просмотр "неусловный" 

Следующие предварительные версии контейнеров доступны в общем доступе. В реестре контейнеров (Майкрософт) (мкр) опубликованы все общедоступные неусловные контейнеры для Cognitive Services. Контейнеры также доступны непосредственно из [центра DOCKER](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Служба | Контейнер | Реестр контейнеров, имя репозитория или образа |
|--|--|--|
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Извлечение ключевых фраз | `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` |
| [Анализ текста](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Распознавание языка | `mcr.microsoft.com/azure-cognitive-services/textanalytics/language` |


#### <a name="gated-preview"></a>Предварительный просмотр "условный"

Ранее контейнеры с условным просмотром были размещены в `containerpreview.azurecr.io` репозитории. Начиная с сентября 22 2020, эти контейнеры (кроме Анализ текста для работоспособности) размещаются в реестре контейнеров Майкрософт (мкр), и их загрузка не требует использования команды DOCKER Login. Чтобы использовать контейнер, необходимо выполнить следующие действия.

1. Заполните [форму запроса](https://aka.ms/csgate) с идентификатором подписки Azure и сценарием пользователя. 
2. После утверждения Скачайте контейнер из мкр. 
3. Используйте ключ и конечную точку из соответствующего ресурса Azure для проверки подлинности контейнера во время выполнения. 

| Служба | Контейнер | Реестр контейнеров, имя репозитория или образа |
|--|--|--|
| [Компьютерное зрение](../../Computer-vision/computer-vision-how-to-install-containers.md) | Read v 3.0 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview` |
| [Компьютерное зрение](../../Computer-vision/computer-vision-how-to-install-containers.md) | Для чтения, версия 3.1 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |
| [Компьютерное зрение](https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-container) | Пространственный анализ | `mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=ctts) | Пользовательский текст в речь | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=lid) | Распознавание языка | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` |
| [API службы "Речь"](../../speech-service/speech-container-howto.md?tab=ntts) | Преобразование текста в речь для нейронов | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` |
| [Анализ текста для работоспособности](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Анализ текста для сферы здравоохранения | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |

