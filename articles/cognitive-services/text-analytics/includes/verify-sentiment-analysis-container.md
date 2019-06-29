---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
description: Узнайте, как проверить экземпляр контейнера анализа тональности.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f68d9c7098f2b1ca782e2522c632c2e267b35336
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455131"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Проверить экземпляр контейнера анализ тональности

1. Выберите **Обзор** вкладку и скопируйте IP-адрес.
1. Откройте новую вкладку браузера и использование IP-адреса, например, `http://<IP-address>:5000 (http://55.55.55.55:5000`). Представленные контейнера домашней страницы, сообщая о контейнер работает.

    ![Просмотр на домашнюю страницу контейнера, чтобы убедиться, что она запущена](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Выберите **описания API службы** ссылку, чтобы перейти на страницу swagger контейнеры.

1. Выберите любую из **POST** API-интерфейсы и выберите **попробовать**.  Отображаются параметры, включая пример ввода:

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

1. Входные данные, замените следующий код JSON:

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

1. Задайте **showStats** значение true.

1. Выберите **Execute** для определения тональности текста.

    Модель, содержащийся в контейнере формирует оценку от 0 до 1, где 0 является отрицательным, а 1 — положительным.

    Ответ JSON, возвращаемый тональности входного текста обновленные включает в себя:

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

Теперь можно использовать документ `id` полезных данных ответа JSON в исходный документ полезных данных запроса `id`и увидеть, что возникла баллов за `.98` , указывающее, является очень положительным тональности.