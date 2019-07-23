---
title: Проверка экземпляра контейнера анализа тональности
titleSuffix: Azure Cognitive Services
description: Узнайте, как проверить экземпляр контейнера анализа тональности.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1303d753b1cbfabe7ddd3442e0880b0bffe089b3
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377383"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Проверка экземпляра контейнера анализа тональности

1. Перейдите на вкладку **Обзор** и скопируйте IP-адрес.
1. Откройте новую вкладку браузера и введите IP-адрес. Например, введите `http://<IP-address>:5000 (http://55.55.55.55:5000`). Откроется домашняя страница контейнера, в которой вы узнаете, что контейнер выполняется.

    ![Просмотрите домашнюю страницу контейнера, чтобы убедиться, что она запущена](../media/how-tos/container-instance/swagger-docs-on-container.png).

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

1. Замените входные данные следующим содержимым JSON:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Задайте для **шовстатс** значение true.

1. Выберите **выполнить** , чтобы определить тональности текста.

    Модель, упакованная в контейнер, формирует оценку в диапазоне от 0 до 1, где 0 является отрицательным, а 1 — положительным.

    Возвращаемый ответ JSON включает тональности для обновленного текстового ввода:

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
        "statistics": {
          "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Теперь можно сопоставить документ `id` JSON полезных данных ответа с исходным документом `id`полезных данных запроса. Оценка больше `.98` указывает на строго положительный тональности.