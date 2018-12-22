---
title: Руководство. Обнаружение аномалий с использованием Python
titlesuffix: Azure Cognitive Services
description: Изучите записную книжку Python, в которой используется API обнаружения аномалий. Отправьте исходные точки данных в API и получите ожидаемое значение и точки аномалий.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: d1e9afc32625cdbf97f576ee091d7dc03271e2fc
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164812"
---
# <a name="tutorial-anomaly-detection-with-python-application"></a>Руководство. Обнаружение аномалий с использованием приложения Python

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

В этом руководстве показано, как использовать API обнаружения аномалий в Python и как визуализировать результаты с использованием популярных библиотек. С помощью Jupyter можно запустить учебное руководство и проверить собственные данные, используя свой ключ подписки. Чтобы узнать, как начать работу с интерактивными записными книжками Jupyter Notebook, обратитесь к [документации по Jupyter](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>Предварительные требования

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Подписка на API обнаружения аномалий и получение ключа подписки 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>Скачивание примера кода

1. Перейдите к [примеру записной книжке на GitHub](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Нажмите зеленую кнопку, чтобы клонировать или скачать пример. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Открытие примера записной книжки в Jupyter

1. Откройте командную строку и перейдите в папку python-sample.
2. Выполните команду Jupyter Notebook из командной строки, которая запустит Jupyter.
3. В окне Jupyter щелкните <em>Anomaly Detection API Example.ipynb</em>, чтобы открыть пример записной книжки.   

## <a name="running-the-tutorial"></a>Запуск руководства

Для использования этой записной книжки вам потребуется ключ подписки на API обнаружения аномалий. Перейдите на страницу Subscription (Подписка), чтобы зарегистрироваться. Выполните вход с помощью учетной записи Майкрософт, после чего вы сможете оформить подписку и получить ключи. После завершения процедуры регистрации вставьте ключ в раздел variables записной книжки (воспроизведен ниже). Подойдет как первичный, так и вторичный ключ. Не забудьте заключить ключ в кавычки, чтобы сделать его строковым значением.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по REST API](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
