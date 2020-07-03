---
title: Проверка экземпляра контейнера распознавание языка
titleSuffix: Azure Cognitive Services
description: Узнайте, как проверить экземпляр контейнера распознавание языка.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: dc52586550f89ddae147d79458584331ed984eea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876462"
---
### <a name="verify-the-language-detection-container-instance"></a>Проверка экземпляра контейнера распознавание языка

1. Перейдите на вкладку **Обзор** и скопируйте IP-адрес.
1. Откройте новую вкладку браузера и введите IP-адрес. Например, введите `http://<IP-address>:5000 (http://55.55.55.55:5000`). Откроется домашняя страница контейнера, в которой вы узнаете, что контейнер выполняется.

    ![Просмотрите домашнюю страницу контейнера, чтобы убедиться, что она запущена](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Выберите ссылку **API службы Description** , чтобы открыть страницу Swagger контейнера.

1. Выберите любой из API-интерфейсов **POST** и нажмите кнопку **попробовать**. Отображаются параметры, включая следующий пример входных данных:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Задайте **showStats** для `true`шовстатс значение.

1. Выберите **выполнить** , чтобы определить тональности текста.

    Модель, упакованная в контейнер, формирует оценку в диапазоне от 0 до 1, где 0 является отрицательным тональности, а 1 — положительным тональности.

    Возвращаемый ответ JSON включает тональности для обновленного текстового ввода:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

Теперь можно сопоставить документы JSON полезных данных ответа с исходными документами полезных данных запроса по соответствующему `id`запросу. Каждый документ обрабатывается независимо с различными статистическими данными `characterCount` , `transactionCount`такими как и. Кроме того, каждый результирующий документ `detectedLanguages` содержит массив с `name`, `iso6391Name`и `score` для каждого обнаруженного языка. При обнаружении `score` нескольких языков используется для определения наиболее вероятного языка.