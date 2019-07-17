---
title: Проверить экземпляр контейнера анализ тональности
titleSuffix: Azure Cognitive Services
description: Узнайте, как проверить экземпляр контейнера анализа тональности.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f69d573e9e70a505018e94cca354f363097cc1b8
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229344"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Проверить экземпляр контейнера анализ тональности

1. Выберите **Обзор** вкладку и скопируйте IP-адрес.
1. Откройте новую вкладку браузера и введите IP-адрес. Например, введите `http://<IP-address>:5000 (http://55.55.55.55:5000`). Отображение домашней страницы контейнера, сообщая о контейнер работает.

    ![Представление контейнера домашнюю страницу, чтобы проверить, что он работает](../media/how-tos/container-instance/swagger-docs-on-container.png).

1. Выберите **описания API службы** ссылку, чтобы перейти на страницу swagger контейнера.

1. Выберите любую из **POST** API-интерфейсы и выберите **попробовать**.  Отображаются параметры, включая этот пример входных данных:

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

1. Замените следующее содержимое JSON входные данные:

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

    Ответ JSON, который возвращается тональности входного текста обновленные включает в себя:

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

Теперь можно использовать документ `id` данных полезные данные ответа JSON в исходный документ полезных данных запроса `id`. Мы видим показатель более `.98`, указывающее на строго положительное мнение.